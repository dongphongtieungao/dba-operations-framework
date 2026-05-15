# Master DBA Runbook – Backend Java → Azure SQL

**Author:** DBA Training Series  
**Version:** 2.0  
**Target Audience:** Backend Java Developer · Junior DBA · DevOps/SRE · New Member DBA  
**Mục tiêu:** Trang bị kiến thức production-grade để vận hành, điều tra sự cố và tối ưu Azure SQL Database một cách chuyên nghiệp.

---

## Mục Lục

1. [DBA Mindset](#1-dba-mindset)
2. [SQL Server Architecture](#2-sql-server-architecture)
3. [DAY 1 – SQL Fundamentals + IO](#3-day-1--sql-fundamentals--io)
4. [DAY 2 – Execution Plan + Index](#4-day-2--execution-plan--index)
5. [DAY 3 – Blocking + Deadlock](#5-day-3--blocking--deadlock)
6. [DAY 4 – Query Store + Parameter Sniffing](#6-day-4--query-store--parameter-sniffing)
7. [DAY 5 – Azure SQL Database](#7-day-5--azure-sql-database)
8. [DAY 6 – Monitoring + Observability](#8-day-6--monitoring--observability)
9. [DAY 7 – Incident Response + RCA](#9-day-7--incident-response--rca)
10. [Production Golden Rules](#10-production-golden-rules)
11. [Golden DBA Checklists](#11-golden-dba-checklists)

---

## 1. DBA Mindset

### DBA là gì?

DBA (Database Administrator) không chỉ là người "viết query". DBA là người chịu trách nhiệm toàn diện về database trong production:

| Trách nhiệm | Mô tả |
|---|---|
| **Stability** | Đảm bảo database hoạt động ổn định 24/7 |
| **Performance** | Theo dõi và tối ưu hiệu năng liên tục |
| **Investigation** | Điều tra sự cố nhanh, chính xác |
| **Backup/Restore** | Đảm bảo data luôn có thể phục hồi |
| **Query Optimization** | Tối ưu query để giảm IO, CPU |
| **Security** | Bảo mật data, phân quyền đúng |
| **Production Management** | Quản trị database trên môi trường production |

---

### Sai Lầm Phổ Biến Nhất

```
Query chậm → thêm index  ← SAI
```

**DBA thực tế phải hỏi trước khi làm bất cứ điều gì:**

- Query chậm vì **CPU** quá cao?
- Hay vì **IO** – đọc quá nhiều data từ disk?
- Hay vì **Blocking** – session đang bị lock chờ?
- Hay vì **Memory** – buffer pool không đủ?
- Hay vì **Network** – latency giữa app và DB?
- Hay vì **App Connection Pool** – pool hết, query xếp hàng chờ?

> **Nguyên tắc vàng:** Không bao giờ đoán. Luôn thu thập evidence trước.

---

### Production Mindset – Quy Trình Tư Duy

Khi có sự cố hoặc câu hỏi về hiệu năng, luôn theo thứ tự:

```
1. Thu thập evidence   → metric, log, wait stats
2. Đọc metric          → CPU, IO, Memory, Sessions
3. Đọc execution plan  → xem SQL làm gì
4. Xác định root cause → bottleneck thực sự ở đâu
5. Mitigate trước      → giảm impact ngay lập tức
6. RCA sau             → phân tích kỹ, viết báo cáo
```

> **Không được restart SQL, kill session bừa, hay thêm index ngay** khi chưa hiểu root cause.

---

## 2. SQL Server Architecture

### Query Execution Flow

Hiểu flow này giúp bạn biết cần debug ở tầng nào khi có vấn đề:

```
Java Application
      ↓
JDBC Driver  (network call, connection pool)
      ↓
SQL Server – Parser  (kiểm tra syntax)
      ↓
SQL Server – Algebrizer  (kiểm tra object, permissions)
      ↓
Query Optimizer  (tìm execution plan tốt nhất)
      ↓
Execution Engine  (thực thi plan)
      ↓
Storage Engine  (đọc/ghi data)
      ↓
Buffer Pool (RAM)  hoặc  Data File (Disk)
```

---

### Các Thành Phần Quan Trọng

#### Query Optimizer
Bộ não của SQL Server. Nhận query → phân tích statistics → tính toán nhiều plan khả năng → chọn plan có estimated cost thấp nhất.

**Ví dụ:**
```sql
SELECT * FROM Orders WHERE CustomerId = 1
```
Optimizer quyết định:
- **Table Scan** – đọc toàn bộ 10 triệu rows? → Tốn IO
- **Index Seek** – nhảy thẳng đến rows cần thiết? → Nhanh

Optimizer có thể chọn sai khi **statistics lỗi thời** hoặc khi **parameter sniffing** xảy ra.

---

#### Execution Plan
Kế hoạch SQL Server dùng để thực thi query. Giống Google Maps chỉ đường – có thể tối ưu hoặc không tối ưu tùy vào thông tin có được lúc compile.

- **Estimated Plan:** Plan được tính trước khi chạy
- **Actual Plan:** Plan thực tế sau khi chạy (có row count thực tế)

---

#### Storage Engine
Tầng thực sự đọc/ghi data:
- **Buffer Pool (RAM):** Cache data page trong memory. Data đã có trong RAM → query rất nhanh.
- **Data File (.mdf/.ndf):** Lưu data trên disk. Phải đọc disk → chậm hơn RAM nhiều lần.
- **Transaction Log (.ldf):** Ghi lại mọi thay đổi. Dùng cho recovery, HA, CDC.

---

#### Buffer Pool
RAM cache trung tâm của SQL Server. Mọi data đều phải qua Buffer Pool trước khi query đọc được.

```
Data trong Buffer Pool (RAM) → Logical Read  → Nhanh
Data chưa có trong RAM       → Physical Read → Phải đọc disk, chậm hơn
```

**Buffer Pool Hit Ratio** thấp = SQL phải đọc disk nhiều = cần tăng RAM hoặc giảm lượng data cần đọc.

---

## 3. DAY 1 – SQL Fundamentals + IO

### Mục Tiêu
Sau ngày này bạn phải hiểu:
- Logical Reads và Physical Reads là gì
- Cách đo IO của một query
- Index cơ bản
- Cách phát hiện IO bottleneck

---

### Lab Setup

Tạo database và bảng thực hành:

```sql
-- Tạo database
CREATE DATABASE DBA_LAB;
GO

USE DBA_LAB;
GO

-- Tạo bảng Orders
CREATE TABLE Orders
(
    Id          BIGINT IDENTITY PRIMARY KEY,
    CustomerId  INT,
    Amount      MONEY,
    Status      VARCHAR(20),
    CreatedDate DATETIME
);
GO
```

Insert 100,000 rows dữ liệu test:

```sql
SET NOCOUNT ON;

DECLARE @i INT = 1;

WHILE @i <= 100000
BEGIN
    INSERT INTO Orders (CustomerId, Amount, Status, CreatedDate)
    VALUES (
        RAND() * 10000,
        RAND() * 5000,
        CASE WHEN @i % 2 = 0 THEN 'PAID' ELSE 'NEW' END,
        GETDATE()
    );
    SET @i += 1;
END
GO
```

> **Lưu ý:** Trên production không dùng WHILE loop như này để insert số lượng lớn. Dùng batch insert hoặc BULK INSERT thay thế.

---

### SET STATISTICS IO – Đo Lường IO

Đây là công cụ cơ bản nhất của DBA khi điều tra query chậm:

```sql
SET STATISTICS IO ON;
SET STATISTICS TIME ON;

-- Chạy query cần điều tra
SELECT * FROM Orders WHERE CustomerId = 10;
```

**Ví dụ output:**
```
SQL Server parse and compile time:
   CPU time = 0 ms, elapsed time = 1 ms.

Table 'Orders'. Scan count 1, logical reads 15000, physical reads 0,
read-ahead reads 0, lob logical reads 0.

SQL Server Execution Times:
   CPU time = 312 ms, elapsed time = 890 ms.
```

---

### Đọc Hiểu STATISTICS IO

| Metric | Ý nghĩa | Dấu hiệu xấu |
|---|---|---|
| **logical reads** | Số page đọc từ Buffer Pool (RAM) | Cao (> vài nghìn cho query đơn giản) |
| **physical reads** | Số page phải đọc từ disk | > 0 nghĩa là data chưa cache |
| **read-ahead reads** | SQL prefetch data vào buffer trước | Số lớn = query scan nhiều |
| **CPU time** | Thời gian CPU thực tế xử lý | Tỉ lệ cao so với elapsed = CPU bottleneck |
| **elapsed time** | Thời gian wall-clock thực tế | Người dùng cảm nhận độ trễ này |

**Công thức tính lượng data đọc:**
```
Logical Reads × 8 KB = Tổng data đọc (bytes)
15,000 reads × 8 KB = 120,000 KB ≈ 117 MB
```

---

### Tại Sao Logical Reads Quan Trọng?

```
Logical Reads cao
        ↓
Query đọc quá nhiều data pages
        ↓
IO lớn → tốn băng thông storage
        ↓
CPU tăng (phải process nhiều data)
        ↓
Cache pressure (đẩy data hữu ích khác ra khỏi Buffer Pool)
        ↓
Physical reads tăng (data bị đẩy ra phải đọc lại từ disk)
        ↓
Hệ thống chậm tổng thể
```

---

### Physical Reads – Kẻ Thù Của Performance

Physical read xảy ra khi:
1. SQL Server restart (Buffer Pool bị clear)
2. Data mới chưa từng đọc vào cache
3. Buffer Pool đầy, SQL phải evict pages cũ

**Disk luôn chậm hơn RAM:**
- RAM latency: ~100 nanoseconds
- SSD latency: ~100 microseconds (1,000x chậm hơn)
- HDD latency: ~10 milliseconds (100,000x chậm hơn)

---

### Checklist Khi Query Chậm (Bước Cơ Bản)

```sql
-- Bước 1: Bật IO và TIME statistics
SET STATISTICS IO ON;
SET STATISTICS TIME ON;

-- Bước 2: Chạy query cần kiểm tra
SELECT ...

-- Bước 3: Phân tích output
-- Xem: logical reads, physical reads, CPU time, elapsed time

-- Bước 4: Mở Actual Execution Plan (Ctrl+M trong SSMS)
-- Xem: Table Scan, Key Lookup, Sort, Hash Match

-- Bước 5: Kiểm tra wait stats của query đó
-- (Xem phần Wait Statistics ở DAY 6)
```

---

## 4. DAY 2 – Execution Plan + Index

### Index – Khái Niệm Cơ Bản

Index giống mục lục của cuốn sách:
- **Không có index:** Phải đọc từng trang từ đầu đến cuối (Table Scan)
- **Có index:** Tra mục lục → nhảy thẳng đến trang cần (Index Seek)

```
100,000 rows, muốn tìm CustomerId = 42:

Table Scan:  đọc tất cả 100,000 rows  → logical reads: 15,000
Index Seek:  nhảy thẳng đến kết quả  → logical reads: 5
```

---

### Clustered Index

**Khái niệm:**
- Xác định thứ tự vật lý của data trong table
- Mỗi table **chỉ có đúng 1** clustered index
- Data page được sắp xếp theo key của clustered index
- Thường là `PRIMARY KEY` (BIGINT IDENTITY)

```sql
-- Khi tạo PRIMARY KEY, SQL tự tạo clustered index
CREATE TABLE Orders
(
    Id BIGINT IDENTITY PRIMARY KEY,  -- ← Clustered Index
    ...
);
```

**Tại sao quan trọng:** Key lookup sẽ quay về clustered index để lấy thêm column. Clustered index lớn = key lookup tốn kém hơn.

---

### Nonclustered Index

**Khái niệm:**
- Bảng tra cứu phụ, tách biệt với data chính
- Một table có thể có **nhiều** nonclustered index
- Lưu key column + pointer về clustered index row

```sql
-- Tạo nonclustered index trên CustomerId
CREATE INDEX IX_Orders_CustomerId
ON Orders(CustomerId);

-- Query này sẽ dùng index seek thay vì table scan
SELECT * FROM Orders WHERE CustomerId = 42;
```

---

### 4 Execution Plan Operators Phổ Biến

#### 1. Table Scan / Index Scan

```
Ý nghĩa:  SQL đọc toàn bộ table hoặc toàn bộ index
Dấu hiệu: Logical reads rất cao
Nguyên nhân:
  - Không có index phù hợp
  - Index có nhưng không selective (quá nhiều row trùng)
  - Query có implicit conversion (kiểu dữ liệu không khớp)
  - Statistics lỗi thời khiến optimizer chọn sai
```

> **Không phải Scan luôn là xấu.** Nếu query cần 80% data của table, Scan có thể tốt hơn Seek.

---

#### 2. Index Seek

```
Ý nghĩa:  SQL đọc đúng rows cần thiết qua B-tree của index
Dấu hiệu: Logical reads thấp
Đây là plan TỐT nhất cho selective queries
```

---

#### 3. Key Lookup (Bookmark Lookup)

```
Ý nghĩa:  Index có nhưng không chứa đủ column cần SELECT
          SQL phải:
            1. Seek trên nonclustered index → lấy clustered key
            2. Quay về clustered index (key lookup) → lấy column còn thiếu

Dấu hiệu:
  - Có mũi tên giữa Index Seek và Key Lookup trong execution plan
  - Tỉ lệ cost của Key Lookup cao

Khi nào xấu: Xảy ra nhiều lần (hàng trăm nghìn lần/query)
Fix: Tạo Covering Index (dùng INCLUDE)
```

**Ví dụ Key Lookup:**
```sql
-- Index chỉ có CustomerId
CREATE INDEX IX_Orders_CustomerId ON Orders(CustomerId);

-- Query này SELECT thêm Amount, Status → Key Lookup xảy ra
SELECT Amount, Status
FROM Orders
WHERE CustomerId = 10;
```

**Fix bằng Covering Index:**
```sql
-- Thêm Amount và Status vào INCLUDE
CREATE INDEX IX_Orders_CustomerId
ON Orders(CustomerId)
INCLUDE (Amount, Status);  -- ← Covering Index

-- Bây giờ SQL không cần Key Lookup nữa
```

---

#### 4. Hash Match

```
Ý nghĩa:  SQL dùng hash table để thực hiện JOIN hoặc GROUP BY
          khi không có index phù hợp hoặc data quá lớn

Dấu hiệu:
  - Memory grant lớn
  - Có cảnh báo "Spill to TempDB" nếu thiếu RAM

Khi nào xuất hiện:
  - JOIN hai bảng lớn không có index phù hợp
  - GROUP BY / ORDER BY trên nhiều rows
  - DISTINCT trên tập lớn

Fix: Thêm index phù hợp cho JOIN condition, review query logic
```

---

#### 5. Sort Operator

```
Ý nghĩa:  SQL phải sắp xếp data (ORDER BY, GROUP BY, DISTINCT)
Dấu hiệu: Memory grant lớn, có thể spill TempDB
Fix: Tạo index đã được sorted theo chiều cần thiết
```

---

### Warning Cần Chú Ý Trong Execution Plan

| Warning | Ý nghĩa | Hành động |
|---|---|---|
| **Spill to TempDB** | Thiếu RAM, SQL viết data tạm ra TempDB | Tune query, thêm RAM, sửa statistics |
| **Implicit Conversion** | Kiểu dữ liệu không khớp, index không dùng được | Sửa data type trong query/schema |
| **No Join Predicate** | JOIN không có điều kiện → Cartesian product | Kiểm tra lại JOIN condition |
| **Missing Index** | SQL gợi ý index nên tạo | Đánh giá trước khi tạo |
| **Row Count Estimate Way Off** | Statistics lỗi thời hoặc parameter sniffing | Update statistics, điều tra sniffing |

---

### Checklist Đọc Execution Plan

```
Step 1: Đọc từ phải sang trái, từ trên xuống dưới
        → Data flow theo hướng đó

Step 2: Tìm operator có % cost cao nhất
        → Đây là bottleneck

Step 3: Tìm các operator đáng lo:
        □ Table Scan / Index Scan (trên bảng lớn)
        □ Key Lookup (xảy ra nhiều lần)
        □ Sort (memory grant lớn)
        □ Hash Match (memory grant lớn)

Step 4: Check tất cả warning (icon hình tam giác vàng):
        □ Spill to TempDB
        □ Implicit Conversion
        □ Missing Index suggestion

Step 5: So sánh Estimated Rows vs Actual Rows
        □ Lệch > 10x → statistics vấn đề hoặc parameter sniffing
        □ Estimated = 1 nhưng Actual = 1,000,000 → rất nguy hiểm
```

---

## 5. DAY 3 – Blocking + Deadlock

### Lock – Cơ Chế Bảo Vệ Data

SQL Server dùng lock để đảm bảo **data consistency** (ACID). Lock ngăn nhiều session đọc/ghi data bị conflict.

### Các Loại Lock Cần Biết

| Lock Type | Ký hiệu | Khi nào xuất hiện | Tương thích với |
|---|---|---|---|
| **Shared** | S | SELECT | Shared khác, không tương thích Exclusive |
| **Exclusive** | X | INSERT / UPDATE / DELETE | Không tương thích gì cả |
| **Update** | U | Giai đoạn đầu của UPDATE | Shared, không tương thích Update khác |
| **Intent Shared** | IS | Table-level, báo hiệu sẽ lấy S lock | Nhiều loại |
| **Intent Exclusive** | IX | Table-level, báo hiệu sẽ lấy X lock | IS, IX |
| **Schema Stability** | Sch-S | DDL operation | Hầu hết |

---

### Blocking

#### Khái Niệm
Blocking xảy ra khi **Session A** giữ lock trên một resource, và **Session B** cần lock đó nhưng phải CHỜ cho đến khi Session A release.

```
Session A: BEGIN TRAN → UPDATE Orders SET Amount=999 WHERE Id=1 → (chưa COMMIT)
                                                                        ↑
Session B: UPDATE Orders SET Amount=500 WHERE Id=1  ← BỊ BLOCK, phải chờ
```

Nếu Session A giữ lock quá lâu → Session B timeout → Java app nhận exception → User thấy lỗi.

---

#### Demo Blocking Trong Lab

**Session 1 (Terminal 1):**
```sql
BEGIN TRAN;

UPDATE Orders
SET Amount = 999
WHERE Id = 1;

-- KHÔNG COMMIT ngay. Để session này mở.
```

**Session 2 (Terminal 2):**
```sql
-- Query này sẽ BỊ BLOCK và treo
UPDATE Orders
SET Amount = 500
WHERE Id = 1;
```

**Session 3 (Terminal 3 – quan sát):**
```sql
-- Xem blocking đang xảy ra
SELECT
    r.session_id,
    r.blocking_session_id,
    r.wait_type,
    r.wait_time / 1000.0 AS wait_seconds,
    r.status,
    SUBSTRING(t.text, (r.statement_start_offset/2)+1,
        ((CASE r.statement_end_offset
            WHEN -1 THEN DATALENGTH(t.text)
            ELSE r.statement_end_offset
          END - r.statement_start_offset)/2)+1
    ) AS query_text
FROM sys.dm_exec_requests r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) t
WHERE r.blocking_session_id <> 0;
```

---

#### Trace Blocking

**Cách 1 – sp_who2 (đơn giản, nhanh):**
```sql
EXEC sp_who2;
-- Xem cột BlkBy – session nào đang block ai
```

**Cách 2 – sys.dm_exec_requests (chi tiết hơn):**
```sql
SELECT
    r.session_id,
    r.blocking_session_id,
    r.wait_type,
    r.wait_time,
    r.status,
    DB_NAME(r.database_id) AS database_name,
    r.cpu_time,
    r.total_elapsed_time
FROM sys.dm_exec_requests r
WHERE r.blocking_session_id <> 0;
```

**Cách 3 – Tìm Head Blocker (nguyên nhân gốc):**
```sql
-- Tìm session đầu tiên gây ra blocking chain
SELECT
    s.session_id,
    s.login_name,
    s.host_name,
    s.program_name,
    s.status,
    t.text AS current_query,
    s.last_request_start_time
FROM sys.dm_exec_sessions s
LEFT JOIN sys.dm_exec_requests r ON s.session_id = r.session_id
OUTER APPLY sys.dm_exec_sql_text(r.sql_handle) t
WHERE s.session_id IN (
    -- Lấy tất cả blocking session IDs
    SELECT DISTINCT blocking_session_id
    FROM sys.dm_exec_requests
    WHERE blocking_session_id <> 0
)
AND s.session_id NOT IN (
    SELECT session_id FROM sys.dm_exec_requests
    WHERE blocking_session_id <> 0
);
```

---

### Deadlock

#### Khái Niệm
Deadlock là tình trạng **hai session lock chéo nhau** và không ai nhường ai:

```
Session A giữ lock Row 1 → cần lock Row 2
Session B giữ lock Row 2 → cần lock Row 1

→ Cả hai cùng chờ nhau → Deadlock
→ SQL Server phát hiện → chọn 1 session làm "victim" → rollback transaction đó
→ Session còn lại tiếp tục chạy
```

---

#### Trace Deadlock – Extended Events (Khuyến Nghị Production)

```sql
-- Tạo Extended Events session để capture deadlock
CREATE EVENT SESSION [Capture_Deadlocks] ON SERVER
ADD EVENT sqlserver.xml_deadlock_report
ADD TARGET package0.event_file
(
    SET filename = N'C:\Temp\Deadlocks.xel',
    max_file_size = (50),     -- 50 MB
    max_rollover_files = (5)  -- giữ 5 files
)
WITH (STARTUP_STATE = ON);
GO

ALTER EVENT SESSION [Capture_Deadlocks] ON SERVER STATE = START;
```

**Đọc deadlock đã captured:**
```sql
SELECT
    xdr.value('@timestamp', 'datetime2') AS deadlock_time,
    xdr.query('.') AS deadlock_graph
FROM
(
    SELECT CAST(event_data AS XML) AS event_data
    FROM sys.fn_xe_file_target_read_file(
        'C:\Temp\Deadlocks*.xel', NULL, NULL, NULL
    )
) AS data
CROSS APPLY event_data.nodes('//RingBufferTarget/event') AS XEventData(xdr)
WHERE xdr.value('@name', 'varchar(4000)') = 'xml_deadlock_report';
```

---

#### Đọc Deadlock Graph

Khi mở deadlock graph XML, tìm:
1. **`<victim-list>`** – Session bị chọn để rollback
2. **`<process>`** – Thông tin từng session (query, waitresource)
3. **`<resource-list>`** – Object bị lock (table, index, row)

---

#### Fix Deadlock – 4 Cách

**Cách 1: Access objects cùng thứ tự**
```
SAI:
  Session A: lock table_orders → lock table_inventory
  Session B: lock table_inventory → lock table_orders

ĐÚNG:
  Session A: lock table_inventory → lock table_orders
  Session B: lock table_inventory → lock table_orders
```

**Cách 2: Rút ngắn transaction**
```sql
-- SAI: Gọi API hoặc làm việc nặng trong transaction
BEGIN TRAN;
    UPDATE Orders SET Status='PROCESSING' WHERE Id=@id;
    EXEC sp_CallExternalAPI @id;  -- ← API chậm, giữ lock lâu
    UPDATE Inventory SET Stock=Stock-1 WHERE ...;
COMMIT;

-- ĐÚNG: Làm nhẹ nhất có thể trong transaction
-- Chuẩn bị data trước khi mở transaction
EXEC sp_CallExternalAPI @id;  -- ← Làm trước
BEGIN TRAN;
    UPDATE Orders SET Status='PROCESSING' WHERE Id=@id;
    UPDATE Inventory SET Stock=Stock-1 WHERE ...;
COMMIT;
```

**Cách 3: Proper Index để giảm lock scope**
```sql
-- Thiếu index → SQL lock nhiều rows khi scan
-- Có index → SQL chỉ lock đúng rows cần thiết
CREATE INDEX IX_Orders_CustomerId ON Orders(CustomerId);
```

**Cách 4: READ COMMITTED SNAPSHOT ISOLATION (RCSI)**
```sql
-- Bật RCSI để reader không block writer
ALTER DATABASE DBA_LAB
SET READ_COMMITTED_SNAPSHOT ON;
-- Lưu ý: Cần tempdb đủ mạnh khi dùng RCSI
```

---

### Checklist Điều Tra Blocking

| Step | Action | Query/Tool |
|---|---|---|
| 1 | Xác định có blocking không | `sp_who2` hoặc `sys.dm_exec_requests` |
| 2 | Tìm head blocker | Query tìm session không bị block nhưng đang block người khác |
| 3 | Xem query của head blocker | `sys.dm_exec_sql_text` |
| 4 | Kiểm tra transaction có open lâu không | `sys.dm_tran_active_transactions` |
| 5 | Xem app có giữ connection/transaction không | Kiểm tra HikariCP metrics |
| 6 | Quyết định kill hay không | Chỉ kill khi chắc chắn business impact |

> **Cảnh báo:** KILL session có thể gây rollback lớn. Luôn kiểm tra `sys.dm_tran_active_transactions` trước khi kill.

---

## 6. DAY 4 – Query Store + Parameter Sniffing

### Query Store

#### Khái Niệm
Query Store là **flight recorder** của SQL Server. Nó tự động lưu:
- Lịch sử query text
- Mọi execution plan của query đó qua các phiên bản
- CPU time, duration, IO, memory grant của từng lần chạy

**Ứng dụng thực tế:**
- Phát hiện **plan regression** sau khi release code mới
- Tìm query tiêu tốn nhiều tài nguyên nhất
- Force một plan cũ tốt hơn khi plan mới bị xấu
- Debug parameter sniffing

---

#### Enable Query Store

```sql
-- Enable trên database cụ thể
ALTER DATABASE DBA_LAB
SET QUERY_STORE = ON
(
    OPERATION_MODE = READ_WRITE,
    CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30),
    DATA_FLUSH_INTERVAL_SECONDS = 900,
    MAX_STORAGE_SIZE_MB = 500,
    QUERY_CAPTURE_MODE = AUTO,   -- Chỉ capture queries đủ significant
    SIZE_BASED_CLEANUP_MODE = AUTO
);
GO
```

---

#### Xem Top Queries Tiêu Tốn Tài Nguyên

```sql
-- Top 10 queries tốn CPU nhất (từ Query Store)
SELECT TOP 10
    qt.query_sql_text,
    q.query_id,
    p.plan_id,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_logical_io_reads,
    rs.count_executions,
    rs.last_execution_time
FROM sys.query_store_query q
JOIN sys.query_store_query_text qt ON q.query_text_id = qt.query_text_id
JOIN sys.query_store_plan p ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats rs ON p.plan_id = rs.plan_id
ORDER BY rs.avg_cpu_time DESC;
```

---

#### Force Plan Khi Plan Regression Xảy Ra

```sql
-- Sau khi xác định plan cũ tốt hơn, force nó
EXEC sys.sp_query_store_force_plan
    @query_id = 42,   -- ID từ Query Store
    @plan_id = 5;     -- Plan ID muốn force

-- Unforce khi đã fix xong
EXEC sys.sp_query_store_unforce_plan
    @query_id = 42,
    @plan_id = 5;
```

---

### Parameter Sniffing

#### Khái Niệm
SQL Server **compile và cache** execution plan lần đầu stored procedure được gọi. Lần đầu đó dùng parameter value nào, plan sẽ được tối ưu cho value đó. Các lần sau **reuse plan cũ** dù parameter khác hẳn.

**Vấn đề:** Một plan không thể tối ưu cho mọi giá trị parameter nếu data distribution không đều.

---

#### Ví Dụ Điển Hình

```sql
-- Stored procedure tìm orders theo Status
CREATE PROCEDURE GetOrdersByStatus
    @Status VARCHAR(20)
AS
BEGIN
    SELECT *
    FROM Orders
    WHERE Status = @Status;
END
GO
```

**Data distribution:**
```
Status = 'PAID'  → 49,000,000 rows  (49% data)
Status = 'NEW'   → 100 rows         (0.0001% data)
Status = 'HOLD'  → 1,000 rows       (0.001% data)
```

**Scenario:**
```
1. Lần đầu gọi với @Status = 'PAID'
   → Optimizer thấy 49M rows → chọn Table Scan (đúng)
   → Plan được cache với Table Scan

2. Lần sau gọi với @Status = 'NEW'
   → SQL reuse plan cũ (Table Scan)
   → Nhưng chỉ có 100 rows → Index Seek mới đúng
   → Query chạy rất chậm!
```

---

#### Dấu Hiệu Nhận Biết Parameter Sniffing

- Query lúc nhanh lúc chậm, không có pattern rõ ràng
- Restart SQL Server → query nhanh trở lại (plan bị clear)
- `DBCC FREEPROCCACHE` → query nhanh lại
- Estimated Rows lệch xa Actual Rows trong execution plan
- Query chạy tốt với `OPTION (RECOMPILE)` nhưng chậm không có hint đó

---

#### Trace Parameter Sniffing

```sql
-- Xem plan đang được cache cho stored procedure
SELECT
    qs.execution_count,
    qs.total_elapsed_time / qs.execution_count / 1000.0 AS avg_duration_ms,
    qs.total_logical_reads / qs.execution_count AS avg_logical_reads,
    qp.query_plan,
    qt.text
FROM sys.dm_exec_procedure_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
CROSS APPLY sys.dm_exec_query_plan(qs.plan_handle) qp
WHERE qt.text LIKE '%GetOrdersByStatus%';
```

---

#### Fix Parameter Sniffing – 3 Cách

**Cách 1: OPTION (RECOMPILE)**
```sql
CREATE PROCEDURE GetOrdersByStatus
    @Status VARCHAR(20)
AS
BEGIN
    SELECT *
    FROM Orders
    WHERE Status = @Status
    OPTION (RECOMPILE);  -- Compile mới mỗi lần gọi
END
```

| Ưu điểm | Nhược điểm |
|---|---|
| Plan luôn đúng cho mọi parameter | Compile overhead mỗi lần gọi |
| Đơn giản, dễ implement | CPU tăng với stored proc gọi nhiều |

---

**Cách 2: OPTIMIZE FOR UNKNOWN**
```sql
CREATE PROCEDURE GetOrdersByStatus
    @Status VARCHAR(20)
AS
BEGIN
    SELECT *
    FROM Orders
    WHERE Status = @Status
    OPTION (OPTIMIZE FOR (@Status UNKNOWN));  -- Dùng average selectivity
END
```

| Ưu điểm | Nhược điểm |
|---|---|
| Không có compile overhead | Plan trung bình, không tối ưu cho extreme values |
| Ổn định, không bị sniff | 'PAID' với 49M rows có thể vẫn bị suboptimal |

---

**Cách 3: Dynamic SQL (cho complex cases)**
```sql
CREATE PROCEDURE GetOrdersByStatus
    @Status VARCHAR(20)
AS
BEGIN
    DECLARE @sql NVARCHAR(MAX);
    SET @sql = N'
        SELECT *
        FROM Orders
        WHERE Status = @p_Status
        OPTION (RECOMPILE)';
    
    EXEC sp_executesql @sql, N'@p_Status VARCHAR(20)', @p_Status = @Status;
END
```

---

### Spill to TempDB

#### Khái Niệm
SQL Server cấp **memory grant** cho query trước khi chạy (dựa trên estimated rows). Nếu thực tế cần nhiều hơn → SQL phải **spill data xuống TempDB**.

```
Estimated: 1,000 rows  → Memory grant: 5 MB
Actual:    5,000,000 rows
→ Thiếu RAM → Spill 4.9 GB data xuống TempDB disk
→ Query CHẬM kinh khủng
```

#### Dấu Hiệu
- Warning màu vàng trong execution plan: `"Operator used tempdb to spill data"`
- TempDB usage cao bất thường
- Query đột ngột chậm dù vẫn chạy đúng

#### Root Cause và Fix

| Root Cause | Fix |
|---|---|
| Statistics lỗi thời → estimated rows sai → memory grant nhỏ | `UPDATE STATISTICS table_name WITH FULLSCAN` |
| Query sort/hash lớn | Thêm index để tránh sort, tune query |
| Parameter sniffing → memory grant dựa trên wrong plan | Fix parameter sniffing (xem trên) |
| Server thiếu RAM thực sự | Scale up RAM |

---

## 7. DAY 5 – Azure SQL Database

### Azure SQL là gì?

Azure SQL Database là **Database-as-a-Service (PaaS)** của Microsoft Azure. Bạn không cần quản lý:
- OS và SQL Server patching
- High Availability setup (tự có 99.99% SLA)
- Backup policy (tự động backup full/differential/log)
- Hardware failure (tự heal)

**Bạn chỉ cần quản lý:** Schema, query performance, data, users.

---

### So Sánh SQL Server VM vs Azure SQL

| Tiêu chí | SQL Server trên VM | Azure SQL Database |
|---|---|---|
| **Control** | Full control OS + SQL | Managed, giới hạn một số feature |
| **SQL Agent** | ✅ Đầy đủ | ❌ Không có (dùng Azure Elastic Jobs) |
| **Patching** | Tự quản lý | ✅ Automatic |
| **HA/DR** | Tự setup AG, FCI | ✅ Built-in, multi-AZ |
| **Backup** | Tự cấu hình | ✅ Auto backup, PITR 35 ngày |
| **Scale** | Manual (stop VM, resize) | ✅ Scale compute on-demand |
| **Cost** | Predictable | Pay-per-use |
| **Linked Server** | ✅ | ❌ Hạn chế |
| **CLR** | ✅ | ❌ Không |

---

### Pricing Model – DTU vs vCore

#### DTU (Database Transaction Unit)
```
Bundle package: CPU + IO + Memory gộp chung
Dễ estimate cho workload nhỏ, predictable
Tier: Basic, Standard, Premium

Ví dụ: Standard S3 = 100 DTUs ≈ budget mid-range
```

#### vCore (Virtual Core) – **Khuyến nghị Production**
```
Tách riêng CPU cores và RAM
Có thể chọn: General Purpose, Business Critical, Hyperscale
Transparent: biết mình đang có bao nhiêu core, bao nhiêu RAM

Ví dụ: Business Critical 8 vCore = 8 cores + 40 GB RAM
```

> **Production recommendation:** Dùng vCore để có khả năng tune và scale rõ ràng hơn.

---

### Các Tier Phổ Biến

| Tier | Use Case | HA | Max Storage |
|---|---|---|---|
| **General Purpose** | Workload thông thường | Locally redundant | 4 TB |
| **Business Critical** | High IOPS, low latency, readable secondary | ✅ Built-in replica | 4 TB |
| **Hyperscale** | Database rất lớn, scale-out | ✅ | 100 TB |

---

### Monitoring Azure SQL

#### Metrics Quan Trọng Trên Azure Portal

| Metric | Ý nghĩa | Alert khi |
|---|---|---|
| **CPU percentage** | CPU đang dùng so với tier | > 80% sustained |
| **Data IO percentage** | IO đọc data so với tier limit | > 80% sustained |
| **Log IO percentage** | IO ghi transaction log | > 80% sustained |
| **DTU/vCore percentage** | Tổng resource utilization | > 80% sustained |
| **Workers percentage** | Worker threads đang dùng | > 80% |
| **Sessions percentage** | Connections đang active | > 80% |
| **In-memory OLTP storage** | Nếu dùng In-Memory tables | > 80% |

---

### Connection Issue – Checklist

**Error phổ biến:**
```
com.microsoft.sqlserver.jdbc.SQLServerException:
Cannot open server 'myserver' requested by the login.
Client with IP address '1.2.3.4' is not allowed to access the server.
```

**Checklist debug theo thứ tự:**

```
□ 1. Firewall rules
      - Azure SQL Firewall: Add IP của client/app server
      - Azure SQL → Networking → Add current client IP
      - Nếu là Azure resource: bật "Allow Azure services"

□ 2. Public endpoint vs Private Endpoint
      - Public: Cần đúng firewall rule
      - Private Endpoint: Cần đúng VNet/subnet configuration

□ 3. Authentication
      - SQL Auth: kiểm tra username/password
      - Azure AD Auth: kiểm tra permission Azure AD user

□ 4. Connection String Format (JDBC)
      jdbc:sqlserver://<server>.database.windows.net:1433;
      databaseName=<db>;
      encrypt=true;
      trustServerCertificate=false;
      hostNameInCertificate=*.database.windows.net;
      loginTimeout=30;

□ 5. TLS Version
      - Azure SQL yêu cầu TLS 1.2+
      - JDBC driver cũ có thể không support

□ 6. Connection Pool Settings (HikariCP)
      - maximumPoolSize: không set quá lớn (Azure SQL có giới hạn sessions)
      - connectionTimeout: 30000ms
      - idleTimeout: 600000ms
      - maxLifetime: 1800000ms
```

---

## 8. DAY 6 – Monitoring + Observability

### Wait Statistics – Chìa Khóa Tìm Bottleneck

#### Khái Niệm
SQL Server thread có thể ở 2 trạng thái:
- **RUNNING:** Đang thực sự dùng CPU xử lý
- **WAITING:** Đang chờ resource (IO, lock, memory, network,...)

**Thực tế:** Hầu hết thời gian query đang CHỜ, không phải CHẠY. Wait stats cho biết đang chờ cái gì → đó là bottleneck thực sự.

---

### Top Wait Types Cần Biết

| Wait Type | Ý nghĩa | Root Cause | Fix |
|---|---|---|---|
| **PAGEIOLATCH_SH/EX** | Chờ đọc/ghi data page từ disk | Storage IO chậm, thiếu cache | Thêm RAM, nâng storage, thêm index |
| **CXPACKET** | Chờ parallel threads sync | MAXDOP quá cao, query quá fan-out | Tune MAXDOP, thêm index |
| **LCK_M_X / LCK_M_S** | Chờ lock (blocking) | Session giữ lock lâu | Tìm và fix blocking |
| **WRITELOG** | Chờ ghi vào transaction log | Log IO chậm | Nâng log storage, reduce log activity |
| **RESOURCE_SEMAPHORE** | Chờ cấp memory grant | Quá nhiều query cần RAM | Tune memory grant queries, fix stats |
| **SOS_SCHEDULER_YIELD** | Thread yield CPU cho thread khác | CPU pressure cao | Fix query tốn CPU |
| **ASYNC_NETWORK_IO** | SQL đã xong, chờ client đọc data | App đọc result chậm | Tune app layer, giảm row count trả về |
| **THREADPOOL** | Không đủ worker thread | Quá nhiều concurrent queries | Giảm concurrent, tune MAXDOP |

---

### Xem Wait Statistics

```sql
-- Top waits hiện tại (từ lần restart cuối)
SELECT TOP 20
    wait_type,
    waiting_tasks_count,
    wait_time_ms,
    max_wait_time_ms,
    signal_wait_time_ms,
    wait_time_ms - signal_wait_time_ms AS resource_wait_time_ms,
    ROUND(100.0 * wait_time_ms / SUM(wait_time_ms) OVER(), 2) AS pct
FROM sys.dm_os_wait_stats
WHERE wait_type NOT IN (
    -- Loại bỏ background waits không liên quan
    'SLEEP_TASK', 'BROKER_TO_FLUSH', 'BROKER_TASK_STOP',
    'CLR_AUTO_EVENT', 'DISPATCHER_QUEUE_SEMAPHORE',
    'FT_IFTS_SCHEDULER_IDLE_WAIT', 'HADR_WORK_QUEUE',
    'HADR_FILESTREAM_IOMGR_IOCOMPLETION', 'HADR_CLUSAPI_CALL',
    'HADR_LOGCAPTURE_WAIT', 'HADR_NOTIFICATION_DEQUEUE',
    'HADR_TIMER_TASK', 'HADR_TRANSPORT_DBRLIST',
    'LAZYWRITER_SLEEP', 'LOGMGR_QUEUE', 'ONDEMAND_TASK_QUEUE',
    'REQUEST_FOR_DEADLOCK_SEARCH', 'RESOURCE_QUEUE',
    'SERVER_IDLE_CHECK', 'SLEEP_DBSTARTUP', 'SLEEP_DBTASK',
    'SLEEP_ERRORHANDLING', 'SLEEP_MASTERDBREADY',
    'SLEEP_MASTERMDREADY', 'SLEEP_MASTERUPGRADED',
    'SLEEP_MSDBSTARTUP', 'SLEEP_SYSTEMTASK', 'SLEEP_TEMPDBSTARTUP',
    'SNI_HTTP_ACCEPT', 'SP_SERVER_DIAGNOSTICS_SLEEP',
    'SQLTRACE_BUFFER_FLUSH', 'SQLTRACE_INCREMENTAL_FLUSH_SLEEP',
    'WAITFOR', 'XE_DISPATCHER_WAIT', 'XE_TIMER_EVENT',
    'BROKER_EVENTHANDLER', 'CHECKPOINT_QUEUE'
)
ORDER BY wait_time_ms DESC;
```

---

### Trace Slow Query – Quy Trình

```
Step 1: Query Store – tìm top CPU/duration query
         ↓
Step 2: Lấy execution plan của query đó
         ↓
Step 3: Check logical reads (SET STATISTICS IO ON)
         ↓
Step 4: Check wait stats trong lúc query chạy
         (sys.dm_exec_requests.wait_type)
         ↓
Step 5: Check có blocking không
         (sys.dm_exec_requests.blocking_session_id)
         ↓
Step 6: Check app layer
         - Connection pool metrics
         - Application logs
         - Network latency giữa app và DB
```

---

### DB Slow vs App Slow – Phân Biệt

#### DB Slow (Database là bottleneck)

**Dấu hiệu trên Azure Portal:**
- CPU percentage > 80%
- Data IO percentage > 80%
- Long-running queries trong Query Store
- Wait stats cao: PAGEIOLATCH, LCK_M_X

**Hành động:** Tối ưu query, thêm index, scale compute tier

---

#### App Slow (Application là bottleneck)

**Dấu hiệu:**
- DB metrics bình thường (CPU thấp, IO thấp)
- Connection pool bị exhaust (HikariCP: all connections active)
- Java thread dump: nhiều thread waiting cho DB connection
- Network latency cao giữa app server và DB

**Hành động:** Tune connection pool, fix N+1 query, add caching layer

---

### HikariCP Monitoring (Java Connection Pool)

#### Metrics Cần Theo Dõi

| HikariCP Metric | Ý nghĩa | Alert khi |
|---|---|---|
| `hikaricp.connections.active` | Connections đang thực sự dùng | Gần sát `maximumPoolSize` |
| `hikaricp.connections.idle` | Connections rảnh trong pool | = 0 (pool đang căng) |
| `hikaricp.connections.pending` | Requests đang chờ connection | > 0 (pool full) |
| `hikaricp.connections.timeout` | Request bị timeout chờ connection | > 0 bất kỳ |
| `hikaricp.connections.creation` | Thời gian tạo connection mới | Tăng cao = DB overloaded |

#### Cấu Hình HikariCP Khuyến Nghị

```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 20          # Tuỳ workload, không set quá cao
      minimum-idle: 5
      connection-timeout: 30000      # 30 giây
      idle-timeout: 600000           # 10 phút
      max-lifetime: 1800000          # 30 phút (phải < Azure SQL idle timeout)
      connection-test-query: SELECT 1
      pool-name: HikariPool-DBA-Lab
      leak-detection-threshold: 60000  # Log nếu connection bị giữ > 60s
```

> **Azure SQL có giới hạn max connections theo tier.** `maximumPoolSize` × số app instances phải < giới hạn đó.

---

## 9. DAY 7 – Incident Response + RCA

### Incident Response Flow

Đây là quy trình chuẩn. **Không được bỏ qua bước nào, không được đảo thứ tự.**

```
1. ALERT
   ↓  (Phát hiện vấn đề qua monitoring, user report, alert rule)
2. VERIFY
   ↓  (Xác nhận có incident thật, không phải false alarm)
3. ASSESS SCOPE
   ↓  (Bao nhiêu % user bị ảnh hưởng? API nào? Service nào?)
4. MITIGATE
   ↓  (Hành động giảm impact NGAY. Chưa cần biết root cause)
5. INVESTIGATE
   ↓  (Thu thập evidence, phân tích metric, query, plan)
6. ROOT CAUSE
   ↓  (Xác định nguyên nhân gốc rễ)
7. PREVENTION
      (Hành động ngăn chặn tái diễn)
```

---

### Bước 1-3: Alert → Verify → Assess Scope

```
□ API nào đang bị lỗi/chậm? HTTP 500? Timeout?
□ Toàn bộ hệ thống hay chỉ một số API?
□ Bao nhiêu % users bị ảnh hưởng?
□ Bắt đầu từ khi nào? (so với deployment gần nhất)
□ Chỉ một DB hay nhiều DB?
□ Chỉ một region hay global?
```

---

### Bước 4: Mitigation – Hành Động Ngay

> **Mitigation ≠ Fix.** Mitigation là giảm impact ngay lập tức. Root cause vẫn cần điều tra sau.

| Tình huống | Mitigation Action | Lưu ý |
|---|---|---|
| Blocking chain dài | Kill head blocker session | Kiểm tra transaction size trước khi kill |
| Plan regression sau release | Force plan cũ từ Query Store | `sp_query_store_force_plan` |
| Memory leak / connection leak | Restart app instance | Không restart DB |
| Bad code release | Rollback deployment | Cần CI/CD rollback process |
| CPU spike do query mới | Kill query, disable feature flag | Nếu không rollback được |
| Deadlock tăng đột biến | Giảm concurrency, throttle traffic | Tạm thời, trong khi điều tra |

---

### Bước 5: Investigate – Thu Thập Evidence

```sql
-- 1. Check CPU và resource utilization (Azure SQL DMV)
SELECT
    end_time,
    avg_cpu_percent,
    avg_data_io_percent,
    avg_log_write_percent,
    avg_memory_usage_percent,
    max_worker_percent,
    max_session_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;

-- 2. Top queries theo CPU trong 1 giờ qua (Query Store)
SELECT TOP 10
    qt.query_sql_text,
    q.query_id,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.count_executions,
    rs.last_execution_time
FROM sys.query_store_query q
JOIN sys.query_store_query_text qt ON q.query_text_id = qt.query_text_id
JOIN sys.query_store_plan p ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats rs ON p.plan_id = rs.plan_id
WHERE rs.last_execution_time > DATEADD(HOUR, -1, GETUTCDATE())
ORDER BY rs.avg_cpu_time DESC;

-- 3. Check blocking hiện tại
SELECT
    r.session_id,
    r.blocking_session_id,
    r.wait_type,
    r.wait_time / 1000.0 AS wait_seconds,
    r.status,
    t.text AS query_text
FROM sys.dm_exec_requests r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) t
WHERE r.blocking_session_id <> 0;

-- 4. Check wait stats top hiện tại
SELECT TOP 10 wait_type, wait_time_ms, waiting_tasks_count
FROM sys.dm_os_wait_stats
WHERE wait_type NOT IN ('SLEEP_TASK', 'WAITFOR', 'LAZYWRITER_SLEEP')
ORDER BY wait_time_ms DESC;

-- 5. Check active sessions và queries
SELECT
    s.session_id,
    s.login_name,
    s.status,
    r.cpu_time,
    r.total_elapsed_time / 1000.0 AS elapsed_seconds,
    r.logical_reads,
    t.text AS query_text
FROM sys.dm_exec_sessions s
JOIN sys.dm_exec_requests r ON s.session_id = r.session_id
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) t
WHERE s.is_user_process = 1
ORDER BY r.total_elapsed_time DESC;
```

---

### RCA – Root Cause Analysis

#### RCA Template

```markdown
## Incident RCA Report

**Incident ID:** INC-2024-0315-001
**Date:** 2024-03-15
**Severity:** P1 (Production down)
**Duration:** 09:05 – 09:47 (42 phút)

---

### Timeline

| Thời gian | Sự kiện |
|---|---|
| 09:00 | Release v2.3.1 lên production |
| 09:05 | Alert: CPU 95%, API timeout rate 40% |
| 09:08 | On-call DBA bắt đầu điều tra |
| 09:15 | Xác định: query mới trong release gây table scan |
| 09:20 | Mitigation: force plan cũ từ Query Store |
| 09:25 | CPU giảm về 30%, API timeout giảm về 0% |
| 09:47 | Hệ thống ổn định hoàn toàn |

---

### Impact

- 40% API checkout bị timeout
- ~12,000 users bị ảnh hưởng
- Estimated revenue loss: X VND

---

### Root Cause

Release v2.3.1 thêm một JOIN mới vào query `GetUserOrders`.
Query mới không có index cho điều kiện JOIN
→ Full table scan trên bảng Orders (50M rows)
→ CPU tăng đột biến
→ Blocking chain hình thành
→ API timeout.

---

### Corrective Action (đã thực hiện)

- [ ] Force plan cũ từ Query Store (immediate mitigation)
- [ ] Tạo index `IX_Orders_UserId_CreatedDate` trên staging
- [ ] Test performance trên staging với production-size data
- [ ] Merge hotfix, release lại sau khi test xong

---

### Preventive Action (ngăn tái diễn)

- [ ] Bắt buộc review execution plan trước mọi DB-touching PR
- [ ] Thêm bước "check Query Store regression" vào release checklist
- [ ] Setup alert: Query duration tăng > 3x so với baseline
- [ ] Chạy load test với production data size trên staging

---

### Lessons Learned

1. Index review phải là bước bắt buộc trong code review process
2. Staging environment cần có production-size data để test plan đúng
3. Query Store force plan là công cụ mitigation mạnh, team phải biết dùng
```

---

## 10. Production Golden Rules

### ❌ Không Được Làm

**1. Không restart SQL Server ngay khi có vấn đề**
```
Restart chỉ xóa triệu chứng (cache, connections, blocking).
Root cause vẫn còn đó và sẽ tái diễn.
Sau restart, bạn cũng mất hết evidence (wait stats, Query Store runtime stats reset).
```

**2. Không kill session bừa**
```
Kill session → transaction đang dở bị rollback.
Rollback một transaction lớn có thể mất nhiều phút, thậm chí hàng giờ.
Trong lúc rollback, blocking có thể còn tệ hơn trước.
Luôn kiểm tra: session đang làm gì? Transaction size bao nhiêu?
```

**3. Không thêm index vô tội vạ**
```
Mỗi index thêm vào:
  - Chiếm thêm disk space
  - Làm chậm INSERT/UPDATE/DELETE (phải maintain index)
  - Tốn bộ nhớ trong Buffer Pool
  - Có thể gây query optimizer chọn sai plan

Quy tắc: Chỉ tạo index sau khi có execution plan evidence.
Và luôn test impact trên staging trước.
```

**4. Không đoán root cause**
```
"Query chậm vì thiếu index" → CÓ THỂ SAI
"Chắc là do hết RAM" → CÓ THỂ SAI
Luôn đọc metric, execution plan, wait stats trước khi kết luận.
```

**5. Không gọi API hoặc làm việc nặng trong transaction**
```sql
-- SAI
BEGIN TRAN;
    UPDATE Orders SET Status='PROCESSING' WHERE Id=@id;
    -- Gọi payment API - có thể mất 5-30 giây!
    EXEC xp_cmdshell 'curl -X POST https://payment.api/process';
    UPDATE Orders SET Status='PAID' WHERE Id=@id;
COMMIT;
-- Trong suốt thời gian đó, row Orders bị lock!
```

**6. Không để transaction mở quá lâu**
```
Transaction mở = lock giữ = blocking tiềm năng.
Thiết kế transaction ngắn nhất có thể.
Làm mọi thứ cần thiết ngoài transaction, chỉ BEGIN TRAN khi thực sự cần.
```

---

### ✅ Luôn Làm

**1. Luôn đọc execution plan trước khi kết luận**
```
Không quan trọng bạn nghĩ query đang làm gì.
Hãy xem SQL Server thực sự đang làm gì.
```

**2. Mitigate trước, RCA sau**
```
Ưu tiên 1: Giảm impact cho user ngay lập tức
Ưu tiên 2: Tìm hiểu tại sao (RCA)
Không delay mitigation để "điều tra thêm"
```

**3. Luôn test trên staging với production-size data**
```
Query plan thay đổi hoàn toàn khi data volume thay đổi.
Staging với 1,000 rows ≠ Production với 50,000,000 rows.
```

**4. Document everything trong incident**
```
Timeline chính xác (timestamp từng bước)
Evidence đã thu thập (screenshot metric, query output)
Actions đã thực hiện (và kết quả)
Bài học rút ra
```

---

## 11. Golden DBA Checklists

### Khi Query Chậm

```
□ 1. Check duration và frequency trong Query Store
□ 2. SET STATISTICS IO ON → xem logical reads
□ 3. SET STATISTICS TIME ON → xem CPU time vs elapsed time
□ 4. Mở Actual Execution Plan
□ 5. Tìm operator cost cao nhất
□ 6. Tìm Table Scan, Index Scan, Key Lookup
□ 7. Check warning: Spill, Implicit Conversion
□ 8. So sánh Estimated Rows vs Actual Rows
□ 9. Check wait stats (sys.dm_exec_requests.wait_type)
□ 10. Check blocking (blocking_session_id)
□ 11. Check parameter sniffing (OPTION RECOMPILE test)
□ 12. Check statistics age (sys.dm_db_stats_properties)
```

---

### Khi CPU Cao

```
□ 1. Xác định % CPU và trend (tăng đột ngột hay tăng dần)
□ 2. Query Store → Top queries by CPU time
□ 3. Xem execution plan của top CPU queries
□ 4. Check Table Scan / Index Scan lớn
□ 5. Check parallelism quá cao (CXPACKET wait)
□ 6. Check Missing Index suggestions trong execution plan
□ 7. Check plan regression (so sánh plan hôm nay vs hôm qua)
□ 8. Check có deployment gần đây không
□ 9. Check statistics có outdated không
```

---

### Khi API Timeout

```
□ 1. Check blocking chain (sys.dm_exec_requests)
□ 2. Check connection pool (HikariCP metrics)
□ 3. Check query nào đang chạy lâu
□ 4. Check CPU và IO có cao không
□ 5. Check wait stats (LCK_M_X = blocking, PAGEIOLATCH = IO)
□ 6. Check có deployment gần đây không
□ 7. Check network latency giữa app và DB
□ 8. Check số sessions đang active
□ 9. Check TempDB usage (nếu spill)
```

---

### Khi Deadlock Xảy Ra

```
□ 1. Capture deadlock graph (Extended Events hoặc System Health)
□ 2. Đọc deadlock graph → xác định object bị lock
□ 3. Tìm query của cả hai session trong deadlock
□ 4. Xác định thứ tự access object
□ 5. Kiểm tra transaction scope có cần thu nhỏ không
□ 6. Kiểm tra có missing index không (gây lock scan rộng)
□ 7. Xem xét RCSI nếu reader-writer deadlock
□ 8. Fix code để access objects cùng thứ tự
```

---

### Khi Sau Release (Preventive Check)

```
□ 1. Mở Query Store → Regressed Queries tab
□ 2. So sánh plan của queries bị thay đổi trong release
□ 3. Check top CPU/Duration queries có thay đổi không
□ 4. Check có missing index warning mới không
□ 5. Monitor wait stats 30 phút sau release
□ 6. Monitor connection pool metrics
□ 7. Check error rate và timeout rate trong APM
```

---

## Kết Luận

Sau khi hoàn thành 7 ngày runbook này, bạn có khả năng:

| Kỹ năng | Mô tả |
|---|---|
| **Execution Plan** | Đọc và phân tích được execution plan, tìm bottleneck |
| **Wait Statistics** | Dùng wait stats để xác định bottleneck đúng chỗ |
| **Blocking Trace** | Trace, phân tích và fix blocking trong production |
| **Deadlock** | Capture, đọc và fix deadlock |
| **Parameter Sniffing** | Debug và fix parameter sniffing với các cách phù hợp |
| **Query Store** | Dùng Query Store để tìm query xấu và fix plan regression |
| **Azure SQL** | Hiểu Azure SQL PaaS, monitoring, cấu hình đúng |
| **Incident Response** | Điều tra incident có quy trình, không đoán mò |
| **RCA** | Viết RCA báo cáo chuẩn production |

---

> **"A good DBA doesn't just fix problems. A good DBA prevents them."**

---

*Runbook này cần được cập nhật khi có thay đổi về version SQL Server, Azure SQL tier mới, hoặc best practice mới từ Microsoft.*  
*Version: 2.0 | Last updated: 2025*
