---
id: runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
title: "Runbook thực hành 7 ngày: Java developer đến Junior Azure SQL DBA"
type: output
created: 2026-05-15
updated: 2026-05-15
covers:
  - sources/master-dba-runbook-backend-java-azure-sql
  - sources/sql-server-2022-administration-inside-out
  - sources/azure-database-migration-guides
  - sources/azure-database-migration-service-documentation
  - concepts/dba-production-mindset
  - concepts/sql-server-execution-plan-analysis
  - concepts/sql-server-blocking-and-deadlock-investigation
  - concepts/azure-sql-monitoring-and-observability
  - concepts/incident-response-and-rca
  - concepts/azure-sql-managed-instance
  - concepts/database-migration
  - concepts/migration-assessment
  - concepts/azure-database-migration-service
---

# Runbook thực hành 7 ngày: Java developer đến Junior Azure SQL DBA

## Thông tin khóa học

**Đối tượng:** Java backend developer đã có kinh nghiệm SQL Server 2019.

**Mục tiêu:** Sau 7 ngày, người học có thể làm việc như một Junior Azure SQL DBA trong các tình huống vận hành cơ bản: đọc evidence, điều tra query chậm, phân tích execution plan, xử lý blocking/deadlock, dùng Query Store, giám sát Azure SQL Managed Instance và demo migrate một database từ SQL Server 2019 lên Azure SQL Managed Instance.

**Điểm khác biệt so với runbook cơ bản:** Tài liệu này không dạy lại SQL căn bản. Người học đã biết table, index, stored procedure, transaction và query T-SQL. Vì vậy nội dung tập trung vào tư duy vận hành production, cloud target, migration readiness, cutover và validation.

**Sản phẩm cuối khóa:** Một buổi demo migration có kiểm chứng, gồm baseline trước migration, assessment, migration run sheet, validation report, app smoke test, post-migration monitoring và rollback plan.

---

## Tư duy xuyên suốt

### Junior Azure SQL DBA cần làm gì?

Một Junior Azure SQL DBA không chỉ biết chạy query. Vai trò này đứng giữa database, cloud platform, ứng dụng và vận hành production.

| Năng lực | Mô tả |
|---|---|
| Evidence-first | Không đoán nguyên nhân, luôn thu thập metric, wait, plan, log |
| Production safety | Biết hành động nào có rủi ro: kill session, restart, tạo index, shrink file |
| Performance triage | Phân biệt CPU, IO, lock, memory, plan regression, connection pool |
| Azure awareness | Hiểu Managed Instance, networking, security, cost, monitoring |
| Migration discipline | Biết assessment, rehearsal, validation, cutover, rollback |
| Communication | Viết timeline, RCA, run sheet và báo cáo rõ ràng |

---

### Những điều không được làm trong khóa học

1. Không kết luận "thiếu index" nếu chưa xem execution plan và logical reads.
2. Không restart SQL Server hoặc Managed Instance để "thử xem hết lỗi không".
3. Không kill session khi chưa biết transaction đang rollback hay giữ lock gì.
4. Không migrate database khi chưa có assessment và rollback plan.
5. Không đổi connection string của app khi chưa có smoke test.
6. Không nói "migration thành công" nếu chưa validate dữ liệu, security và performance.
7. Không dùng production data nhạy cảm trong lab nếu chưa được masking hoặc approval.

---

### Quy trình tư duy khi gặp sự cố

```text
Alert hoặc user complaint
        |
        v
Xác minh có lỗi thật không
        |
        v
Xác định phạm vi: app nào, database nào, query nào, user nào
        |
        v
Thu thập evidence: metric, waits, Query Store, plan, log app, connection pool
        |
        v
Mitigate nếu impact cao
        |
        v
RCA: root cause, corrective action, preventive action
```

---

## Môi trường lab chuẩn

### Thành phần bắt buộc

- SQL Server 2019 Developer Edition hoặc Standard/Enterprise lab.
- Một database lab tên `SalesAppDB`.
- Một Azure SQL Managed Instance target.
- Một Java backend nhỏ dùng JDBC/HikariCP.
- SSMS hoặc Azure Data Studio.
- Azure Portal access.
- Azure CLI hoặc PowerShell nếu tổ chức cho phép.
- Azure Database Migration Service hoặc migration path tương đương.

---

### Database lab đề xuất

Nếu chưa có database nội bộ, dùng database lab đơn giản dưới đây. Mục tiêu không phải mô hình nghiệp vụ hoàn hảo, mà là tạo đủ tình huống: query chậm, index thiếu, blocking, deadlock, migration và validation.

```sql
CREATE DATABASE SalesAppDB;
GO

ALTER DATABASE SalesAppDB SET RECOVERY FULL;
GO

USE SalesAppDB;
GO

CREATE TABLE dbo.Customers
(
    CustomerId       INT IDENTITY(1,1) NOT NULL,
    Email            NVARCHAR(320) NOT NULL,
    FullName         NVARCHAR(200) NOT NULL,
    City             NVARCHAR(100) NULL,
    CreatedAt        DATETIME2(3) NOT NULL DEFAULT SYSUTCDATETIME(),
    Status           VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    CONSTRAINT PK_Customers PRIMARY KEY CLUSTERED (CustomerId),
    CONSTRAINT UQ_Customers_Email UNIQUE (Email)
);
GO

CREATE TABLE dbo.Orders
(
    OrderId          BIGINT IDENTITY(1,1) NOT NULL,
    CustomerId       INT NOT NULL,
    OrderStatus      VARCHAR(20) NOT NULL,
    TotalAmount      DECIMAL(18,2) NOT NULL,
    CreatedAt        DATETIME2(3) NOT NULL,
    UpdatedAt        DATETIME2(3) NULL,
    Notes            NVARCHAR(1000) NULL,
    CONSTRAINT PK_Orders PRIMARY KEY CLUSTERED (OrderId),
    CONSTRAINT FK_Orders_Customers FOREIGN KEY (CustomerId)
        REFERENCES dbo.Customers(CustomerId)
);
GO

CREATE TABLE dbo.OrderItems
(
    OrderItemId      BIGINT IDENTITY(1,1) NOT NULL,
    OrderId          BIGINT NOT NULL,
    ProductCode      VARCHAR(50) NOT NULL,
    Quantity         INT NOT NULL,
    UnitPrice        DECIMAL(18,2) NOT NULL,
    CONSTRAINT PK_OrderItems PRIMARY KEY CLUSTERED (OrderItemId),
    CONSTRAINT FK_OrderItems_Orders FOREIGN KEY (OrderId)
        REFERENCES dbo.Orders(OrderId)
);
GO

CREATE TABLE dbo.PaymentEvents
(
    PaymentEventId   BIGINT IDENTITY(1,1) NOT NULL,
    OrderId          BIGINT NOT NULL,
    EventType        VARCHAR(40) NOT NULL,
    ProviderRef      VARCHAR(100) NULL,
    Payload          NVARCHAR(MAX) NULL,
    CreatedAt        DATETIME2(3) NOT NULL DEFAULT SYSUTCDATETIME(),
    CONSTRAINT PK_PaymentEvents PRIMARY KEY CLUSTERED (PaymentEventId)
);
GO
```

---

### Seed data tối thiểu

```sql
USE SalesAppDB;
GO

SET NOCOUNT ON;

DECLARE @i INT = 1;

WHILE @i <= 50000
BEGIN
    INSERT dbo.Customers (Email, FullName, City, CreatedAt, Status)
    VALUES
    (
        CONCAT('user', @i, '@example.com'),
        CONCAT(N'Customer ', @i),
        CASE @i % 5
            WHEN 0 THEN N'Ha Noi'
            WHEN 1 THEN N'Ho Chi Minh'
            WHEN 2 THEN N'Da Nang'
            WHEN 3 THEN N'Can Tho'
            ELSE N'Hai Phong'
        END,
        DATEADD(DAY, -(@i % 365), SYSUTCDATETIME()),
        CASE WHEN @i % 20 = 0 THEN 'INACTIVE' ELSE 'ACTIVE' END
    );

    SET @i += 1;
END;
GO

DECLARE @j INT = 1;

WHILE @j <= 300000
BEGIN
    INSERT dbo.Orders (CustomerId, OrderStatus, TotalAmount, CreatedAt, UpdatedAt, Notes)
    VALUES
    (
        1 + ABS(CHECKSUM(NEWID())) % 50000,
        CASE @j % 4
            WHEN 0 THEN 'NEW'
            WHEN 1 THEN 'PAID'
            WHEN 2 THEN 'SHIPPED'
            ELSE 'CANCELLED'
        END,
        CAST((10 + ABS(CHECKSUM(NEWID())) % 500000) / 10.0 AS DECIMAL(18,2)),
        DATEADD(MINUTE, -(@j % 200000), SYSUTCDATETIME()),
        NULL,
        REPLICATE(N'x', @j % 200)
    );

    SET @j += 1;
END;
GO
```

---

### Query mẫu cho Java API

```sql
CREATE OR ALTER PROCEDURE dbo.GetCustomerOrders
    @CustomerId INT
AS
BEGIN
    SET NOCOUNT ON;

    SELECT TOP (100)
        o.OrderId,
        o.OrderStatus,
        o.TotalAmount,
        o.CreatedAt
    FROM dbo.Orders AS o
    WHERE o.CustomerId = @CustomerId
    ORDER BY o.CreatedAt DESC;
END;
GO

CREATE OR ALTER PROCEDURE dbo.SearchOrdersByStatus
    @OrderStatus VARCHAR(20),
    @FromDate DATETIME2(3),
    @ToDate DATETIME2(3)
AS
BEGIN
    SET NOCOUNT ON;

    SELECT
        o.OrderId,
        o.CustomerId,
        o.TotalAmount,
        o.CreatedAt
    FROM dbo.Orders AS o
    WHERE o.OrderStatus = @OrderStatus
      AND o.CreatedAt >= @FromDate
      AND o.CreatedAt < @ToDate
    ORDER BY o.CreatedAt DESC;
END;
GO

CREATE OR ALTER PROCEDURE dbo.MarkOrderPaid
    @OrderId BIGINT,
    @ProviderRef VARCHAR(100)
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRAN;

        UPDATE dbo.Orders
        SET OrderStatus = 'PAID',
            UpdatedAt = SYSUTCDATETIME()
        WHERE OrderId = @OrderId;

        INSERT dbo.PaymentEvents (OrderId, EventType, ProviderRef, Payload)
        VALUES (@OrderId, 'PAYMENT_CAPTURED', @ProviderRef, N'{}');

    COMMIT;
END;
GO
```

---

### HikariCP cấu hình mẫu

```properties
spring.datasource.url=jdbc:sqlserver://localhost:1433;databaseName=SalesAppDB;encrypt=true;trustServerCertificate=true
spring.datasource.username=app_user
spring.datasource.password=change_me
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
spring.datasource.hikari.leak-detection-threshold=60000
```

---

## Tổng quan 7 ngày

| Ngày | Trọng tâm | Kết quả chính |
|---|---|---|
| 1 | DBA mindset, baseline, architecture | Có baseline trước migration |
| 2 | IO, execution plan, index | Biết phân tích query bằng evidence |
| 3 | Blocking, deadlock, transaction | Biết xử lý API timeout do lock |
| 4 | Query Store, parameter sniffing | Biết phát hiện plan regression |
| 5 | Azure SQL Managed Instance readiness | Có assessment và target design |
| 6 | Migration rehearsal | Chạy thử migration và validation |
| 7 | Demo migration cuối khóa | Cutover demo, smoke test, monitoring |

---

# Ngày 1 - DBA mindset, baseline và kiến trúc vận hành

## Mục tiêu ngày 1

Sau ngày 1, người học phải:

- Mô tả được trách nhiệm của Junior Azure SQL DBA.
- Vẽ được đường đi của request từ Java API tới SQL Server.
- Thu được baseline database trước migration.
- Biết vì sao baseline là điều kiện bắt buộc trước khi migrate.

---

## Bài học 1.1 - DBA không chỉ là người viết query

Developer thường hỏi:

```text
Query này chạy được chưa?
```

DBA production phải hỏi:

```text
Query này chạy ổn định không?
Nó đọc bao nhiêu data?
Nó có block người khác không?
Nó có plan regression không?
Nó có chịu được production data size không?
Nếu migrate lên Azure SQL Managed Instance thì behavior có đổi không?
```

---

## Bài học 1.2 - Những lớp cần quan sát

```text
HTTP request
  |
Java controller/service
  |
JDBC driver
  |
HikariCP connection pool
  |
Network / DNS / firewall
  |
SQL endpoint
  |
SQL parser / optimizer
  |
Execution engine
  |
Storage engine / buffer pool / transaction log
```

Khi API chậm, lỗi có thể nằm ở bất kỳ lớp nào.

Junior DBA không cần sửa mọi lớp, nhưng phải biết thu evidence để không đổ lỗi sai.

---

## Lab 1.1 - Kiểm tra thông tin source instance

Chạy trên SQL Server 2019 source:

```sql
SELECT
    @@SERVERNAME AS server_name,
    @@VERSION AS sql_version,
    SERVERPROPERTY('Edition') AS edition,
    SERVERPROPERTY('ProductVersion') AS product_version,
    SERVERPROPERTY('ProductLevel') AS product_level;
GO
```

Ghi vào baseline:

- Server name.
- SQL Server version.
- Edition.
- Patch level.
- Database name.
- Recovery model.
- Compatibility level.

---

## Lab 1.2 - Kiểm tra database option

```sql
SELECT
    name,
    compatibility_level,
    recovery_model_desc,
    page_verify_option_desc,
    is_auto_create_stats_on,
    is_auto_update_stats_on,
    is_query_store_on
FROM sys.databases
WHERE name = 'SalesAppDB';
GO
```

Kỳ vọng:

- `page_verify_option_desc = CHECKSUM`.
- `is_auto_create_stats_on = 1`.
- `is_auto_update_stats_on = 1`.
- Query Store nên được bật cho lab.

Nếu Query Store chưa bật:

```sql
ALTER DATABASE SalesAppDB SET QUERY_STORE = ON;
GO

ALTER DATABASE SalesAppDB SET QUERY_STORE
(
    OPERATION_MODE = READ_WRITE,
    CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30),
    DATA_FLUSH_INTERVAL_SECONDS = 900,
    INTERVAL_LENGTH_MINUTES = 15,
    MAX_STORAGE_SIZE_MB = 1024,
    QUERY_CAPTURE_MODE = AUTO
);
GO
```

---

## Lab 1.3 - Database size baseline

```sql
USE SalesAppDB;
GO

SELECT
    DB_NAME() AS database_name,
    name AS logical_file_name,
    type_desc,
    physical_name,
    CAST(size * 8.0 / 1024 AS DECIMAL(18,2)) AS size_mb,
    CAST(FILEPROPERTY(name, 'SpaceUsed') * 8.0 / 1024 AS DECIMAL(18,2)) AS used_mb,
    CAST((size - FILEPROPERTY(name, 'SpaceUsed')) * 8.0 / 1024 AS DECIMAL(18,2)) AS free_mb
FROM sys.database_files;
GO
```

Ghi nhận:

- Data file size.
- Log file size.
- Free space.
- Có autogrowth quá nhỏ không.

---

## Lab 1.4 - Top query baseline bằng Query Store

```sql
USE SalesAppDB;
GO

SELECT TOP (10)
    qt.query_sql_text,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_logical_io_reads AS avg_logical_reads,
    rs.count_executions,
    p.plan_id,
    q.query_id
FROM sys.query_store_runtime_stats AS rs
JOIN sys.query_store_plan AS p
    ON rs.plan_id = p.plan_id
JOIN sys.query_store_query AS q
    ON p.query_id = q.query_id
JOIN sys.query_store_query_text AS qt
    ON q.query_text_id = qt.query_text_id
ORDER BY rs.avg_duration DESC;
GO
```

Nếu Query Store chưa có dữ liệu, chạy app Java hoặc chạy stored procedure nhiều lần.

---

## Lab 1.5 - Active session baseline

```sql
SELECT
    r.session_id,
    r.status,
    r.command,
    r.cpu_time,
    r.total_elapsed_time,
    r.logical_reads,
    r.reads,
    r.writes,
    r.wait_type,
    r.wait_time,
    r.blocking_session_id,
    DB_NAME(r.database_id) AS database_name,
    SUBSTRING(t.text, r.statement_start_offset / 2 + 1,
        CASE
            WHEN r.statement_end_offset = -1
            THEN LEN(CONVERT(NVARCHAR(MAX), t.text))
            ELSE (r.statement_end_offset - r.statement_start_offset) / 2 + 1
        END) AS running_statement
FROM sys.dm_exec_requests AS r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) AS t
WHERE r.session_id <> @@SPID
ORDER BY r.total_elapsed_time DESC;
GO
```

---

## Lab 1.6 - App connection pool baseline

Người học cần ghi lại các metric sau từ app:

- HikariCP active connections.
- HikariCP idle connections.
- HikariCP pending threads.
- Connection timeout count.
- API latency p50/p95/p99.
- Error rate.

Nếu chưa có APM, ghi từ log hoặc actuator metrics.

Ví dụ Spring Boot endpoint:

```text
/actuator/metrics/hikaricp.connections.active
/actuator/metrics/hikaricp.connections.idle
/actuator/metrics/hikaricp.connections.pending
/actuator/metrics/http.server.requests
```

---

## Checklist cuối ngày 1

- [ ] Biết database nào sẽ migrate.
- [ ] Biết app Java nào dùng database này.
- [ ] Có baseline SQL Server version và database option.
- [ ] Có database size baseline.
- [ ] Có top query baseline.
- [ ] Có connection pool baseline.
- [ ] Có sơ đồ request path.
- [ ] Có danh sách câu hỏi cần xử lý trước migration.

---

## Mẫu báo cáo ngày 1

```markdown
# Day 1 Baseline Report

## Source
- SQL Server:
- Database:
- Compatibility level:
- Recovery model:
- Query Store:

## Database size
| File | Type | Size MB | Used MB | Free MB |
|---|---|---:|---:|---:|

## Top queries
| Query | Avg duration ms | Avg CPU ms | Avg logical reads | Executions |
|---|---:|---:|---:|---:|

## App metrics
| Metric | Value |
|---|---:|
| Hikari active |
| Hikari idle |
| Hikari pending |
| API p95 |

## Risks before migration
- 
```

---

# Ngày 2 - IO, execution plan và index review

## Mục tiêu ngày 2

Sau ngày 2, người học phải:

- Đọc được `STATISTICS IO`.
- Đọc được actual execution plan ở mức junior.
- Phân biệt được scan, seek, lookup, sort, hash.
- Đề xuất index có bằng chứng.
- Biết vì sao index có thể làm chậm write workload.

---

## Bài học 2.1 - Logical reads là chỉ số rất quan trọng

Logical reads là số page 8 KB SQL Server đọc từ buffer pool.

```text
logical reads x 8 KB = lượng data SQL Server phải xử lý
```

Nếu một query trả về 10 rows nhưng đọc 500.000 pages, vấn đề không nằm ở số rows trả về, mà nằm ở lượng data bị đọc để tìm 10 rows đó.

---

## Lab 2.1 - Đo IO cho query theo customer

```sql
USE SalesAppDB;
GO

SET STATISTICS IO ON;
SET STATISTICS TIME ON;

EXEC dbo.GetCustomerOrders @CustomerId = 12345;

SET STATISTICS IO OFF;
SET STATISTICS TIME OFF;
GO
```

Ghi lại:

- Logical reads.
- Physical reads.
- CPU time.
- Elapsed time.
- Có scan không.
- Có sort không.

---

## Lab 2.2 - Xem index hiện có

```sql
SELECT
    t.name AS table_name,
    i.name AS index_name,
    i.type_desc,
    i.is_primary_key,
    i.is_unique,
    c.name AS column_name,
    ic.key_ordinal,
    ic.is_included_column
FROM sys.tables AS t
JOIN sys.indexes AS i
    ON t.object_id = i.object_id
JOIN sys.index_columns AS ic
    ON i.object_id = ic.object_id
   AND i.index_id = ic.index_id
JOIN sys.columns AS c
    ON ic.object_id = c.object_id
   AND ic.column_id = c.column_id
WHERE t.name IN ('Orders', 'OrderItems', 'Customers')
ORDER BY t.name, i.index_id, ic.key_ordinal, ic.index_column_id;
GO
```

---

## Lab 2.3 - Đề xuất index cho query

Nếu `GetCustomerOrders` scan bảng `Orders`, cân nhắc index:

```sql
CREATE INDEX IX_Orders_CustomerId_CreatedAt
ON dbo.Orders (CustomerId, CreatedAt DESC)
INCLUDE (OrderStatus, TotalAmount);
GO
```

Chạy lại:

```sql
SET STATISTICS IO ON;
SET STATISTICS TIME ON;

EXEC dbo.GetCustomerOrders @CustomerId = 12345;

SET STATISTICS IO OFF;
SET STATISTICS TIME OFF;
GO
```

So sánh trước/sau:

| Metric | Trước index | Sau index |
|---|---:|---:|
| Logical reads | | |
| CPU ms | | |
| Elapsed ms | | |
| Main operator | | |

---

## Bài học 2.2 - Không blindly tin missing index suggestion

Missing index suggestion trong plan là tín hiệu tham khảo, không phải lệnh phải chạy.

Trước khi tạo index:

- Query này chạy thường xuyên không?
- Logical reads có cao không?
- Index có trùng index khác không?
- Có làm chậm insert/update/delete không?
- Có tăng storage đáng kể không?
- Có test trên data đủ lớn chưa?
- Có rollback script chưa?

Rollback script:

```sql
DROP INDEX IX_Orders_CustomerId_CreatedAt ON dbo.Orders;
GO
```

---

## Lab 2.4 - Tìm unused hoặc duplicate index

```sql
SELECT
    OBJECT_NAME(i.object_id) AS table_name,
    i.name AS index_name,
    s.user_seeks,
    s.user_scans,
    s.user_lookups,
    s.user_updates
FROM sys.indexes AS i
LEFT JOIN sys.dm_db_index_usage_stats AS s
    ON i.object_id = s.object_id
   AND i.index_id = s.index_id
   AND s.database_id = DB_ID()
WHERE OBJECTPROPERTY(i.object_id, 'IsUserTable') = 1
ORDER BY s.user_updates DESC;
GO
```

Không drop index chỉ vì `user_seeks = 0` trong lab ngắn. Trong production cần đủ chu kỳ workload.

---

## Lab 2.5 - Kiểm tra statistics age

```sql
SELECT
    OBJECT_NAME(s.object_id) AS table_name,
    s.name AS stats_name,
    sp.last_updated,
    sp.rows,
    sp.rows_sampled,
    sp.modification_counter
FROM sys.stats AS s
CROSS APPLY sys.dm_db_stats_properties(s.object_id, s.stats_id) AS sp
WHERE OBJECTPROPERTY(s.object_id, 'IsUserTable') = 1
ORDER BY sp.modification_counter DESC;
GO
```

Nếu statistics quá cũ trong lab:

```sql
UPDATE STATISTICS dbo.Orders WITH FULLSCAN;
GO
```

Trong production, không mặc định dùng `FULLSCAN` cho bảng lớn nếu chưa đánh giá chi phí.

---

## Checklist đọc execution plan

- [ ] Query trả về bao nhiêu rows?
- [ ] Query đọc bao nhiêu logical reads?
- [ ] Operator tốn chi phí chính là gì?
- [ ] Có Table Scan hoặc Index Scan lớn không?
- [ ] Có Key Lookup lặp lại nhiều lần không?
- [ ] Có Sort lớn không?
- [ ] Có Hash Match lớn không?
- [ ] Có warning spill to TempDB không?
- [ ] Có implicit conversion không?
- [ ] Estimated rows và actual rows lệch nhiều không?
- [ ] Parameter nào được sniff lúc compile?
- [ ] Query có cần index mới, sửa query hay update stats?

---

## Deliverable ngày 2

```markdown
# Day 2 Query Tuning Report

## Query 1
- Procedure:
- Business purpose:
- Frequency:
- Before logical reads:
- Before elapsed:
- Plan issue:
- Proposed action:
- After logical reads:
- After elapsed:
- Risk:
- Rollback:

## Index proposal
```sql
-- CREATE INDEX ...
```

## Evidence
- Screenshot or plan file:
- STATISTICS IO:
- Query Store query_id:
```

---

# Ngày 3 - Blocking, deadlock và transaction trong Java app

## Mục tiêu ngày 3

Sau ngày 3, người học phải:

- Tạo và điều tra được blocking.
- Tạo và đọc được deadlock cơ bản.
- Hiểu transaction scope trong Java ảnh hưởng tới lock.
- Biết khi nào cần sửa code thay vì chỉ sửa database.

---

## Bài học 3.1 - Blocking không luôn là lỗi

Blocking là hành vi bình thường khi một transaction giữ lock và transaction khác cần resource đó.

Blocking trở thành sự cố khi:

- Lock giữ quá lâu.
- API timeout.
- Connection pool pending tăng.
- Blocking chain lan rộng.
- Job hoặc batch giữ transaction lớn.

---

## Lab 3.1 - Tạo blocking

Session 1:

```sql
USE SalesAppDB;
GO

BEGIN TRAN;

UPDATE dbo.Orders
SET Notes = N'blocking demo'
WHERE OrderId = 1000;

-- Không commit ngay.
```

Session 2:

```sql
USE SalesAppDB;
GO

UPDATE dbo.Orders
SET Notes = N'blocked session'
WHERE OrderId = 1000;
```

Session 3 để quan sát:

```sql
SELECT
    r.session_id,
    r.status,
    r.wait_type,
    r.wait_time,
    r.blocking_session_id,
    r.command,
    t.text
FROM sys.dm_exec_requests AS r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) AS t
WHERE r.blocking_session_id <> 0
   OR r.session_id IN
   (
       SELECT blocking_session_id
       FROM sys.dm_exec_requests
       WHERE blocking_session_id <> 0
   );
GO
```

Kết thúc lab:

```sql
-- Session 1
ROLLBACK;
GO
```

---

## Lab 3.2 - Xem transaction đang mở

```sql
SELECT
    s.session_id,
    s.login_name,
    s.host_name,
    s.program_name,
    at.transaction_begin_time,
    DATEDIFF(SECOND, at.transaction_begin_time, SYSUTCDATETIME()) AS tran_age_seconds,
    dt.database_transaction_log_bytes_used,
    dt.database_transaction_log_bytes_reserved
FROM sys.dm_tran_session_transactions AS st
JOIN sys.dm_tran_active_transactions AS at
    ON st.transaction_id = at.transaction_id
JOIN sys.dm_tran_database_transactions AS dt
    ON st.transaction_id = dt.transaction_id
JOIN sys.dm_exec_sessions AS s
    ON st.session_id = s.session_id
ORDER BY tran_age_seconds DESC;
GO
```

---

## Lab 3.3 - Tạo deadlock

Session 1:

```sql
USE SalesAppDB;
GO

BEGIN TRAN;

UPDATE dbo.Customers
SET City = N'Ha Noi'
WHERE CustomerId = 1;

WAITFOR DELAY '00:00:05';

UPDATE dbo.Orders
SET Notes = N'deadlock session 1'
WHERE OrderId = 1;

COMMIT;
GO
```

Session 2 chạy gần như đồng thời:

```sql
USE SalesAppDB;
GO

BEGIN TRAN;

UPDATE dbo.Orders
SET Notes = N'deadlock session 2'
WHERE OrderId = 1;

WAITFOR DELAY '00:00:05';

UPDATE dbo.Customers
SET City = N'Da Nang'
WHERE CustomerId = 1;

COMMIT;
GO
```

Một session sẽ bị chọn làm victim.

---

## Lab 3.4 - Đọc deadlock từ system health

```sql
WITH xevents AS
(
    SELECT CAST(event_data AS XML) AS event_data
    FROM sys.fn_xe_file_target_read_file
    (
        N'system_health*.xel',
        NULL,
        NULL,
        NULL
    )
    WHERE object_name = 'xml_deadlock_report'
)
SELECT TOP (5)
    event_data
FROM xevents
ORDER BY event_data.value('(event/@timestamp)[1]', 'datetime2') DESC;
GO
```

Nếu không đọc được file trên môi trường hạn chế, dùng Extended Events session riêng trong lab.

---

## Bài học 3.2 - Pattern nguy hiểm trong Java

Pseudo-code nguy hiểm:

```java
@Transactional
public void payOrder(long orderId) {
    orderRepository.markProcessing(orderId);
    paymentGateway.capture(orderId); // call network trong transaction
    orderRepository.markPaid(orderId);
}
```

Vấn đề:

- Row trong `Orders` bị lock trong lúc chờ payment gateway.
- Nếu gateway chậm 10 giây, lock giữ 10 giây.
- Nếu nhiều request đồng thời, connection pool có thể cạn.
- API timeout có thể tạo retry và làm lock tệ hơn.

Pattern tốt hơn:

```java
public void payOrder(long orderId) {
    paymentGateway.capture(orderId);

    transactionTemplate.execute(status -> {
        orderRepository.markPaid(orderId);
        paymentEventRepository.insertCaptured(orderId);
        return null;
    });
}
```

Không phải mọi flow đều sửa được như vậy, nhưng DBA phải biết đặt câu hỏi về transaction boundary.

---

## Checklist điều tra blocking

- [ ] Session nào đang bị chờ?
- [ ] Session nào đang block?
- [ ] Wait type là gì?
- [ ] Object nào bị lock?
- [ ] Query text của blocker là gì?
- [ ] Transaction đã mở bao lâu?
- [ ] Log bytes used lớn không?
- [ ] App nào, host nào, login nào?
- [ ] Có deployment hoặc batch job đang chạy không?
- [ ] Có thể chờ tự hết không?
- [ ] Nếu kill thì rollback mất bao lâu?
- [ ] Fix dài hạn là index, query, transaction scope hay retry policy?

---

## Checklist điều tra deadlock

- [ ] Lấy deadlock graph.
- [ ] Xác định victim.
- [ ] Xác định process 1 và process 2.
- [ ] Xác định object/resource bị tranh chấp.
- [ ] Xác định thứ tự access object.
- [ ] Có scan rộng làm lock nhiều row không?
- [ ] Có transaction dài không?
- [ ] Có retry logic trong app không?
- [ ] Có thể thống nhất thứ tự update không?
- [ ] Có thể thêm index để giảm lock footprint không?
- [ ] Có thể bật row versioning/RCSI không, nếu phù hợp và được review?

---

## Deliverable ngày 3

```markdown
# Day 3 Blocking and Deadlock Report

## Blocking case
- Blocked session:
- Blocking session:
- Wait type:
- Object:
- Query text:
- Transaction age:
- Mitigation:
- Long-term fix:

## Deadlock case
- Victim:
- Resource:
- Query A:
- Query B:
- Root cause:
- Fix:
- App change needed:
```

---

# Ngày 4 - Query Store, parameter sniffing và plan regression

## Mục tiêu ngày 4

Sau ngày 4, người học phải:

- Dùng Query Store để tìm top query.
- Nhận diện query có nhiều plan.
- Mô tả parameter sniffing ở mức vận hành.
- Biết force plan là mitigation, không phải fix cuối cùng.
- Chuẩn bị dashboard sau migration.

---

## Bài học 4.1 - Query Store là nhật ký hiệu năng của database

Query Store giúp trả lời:

- Query nào chậm nhất?
- Query nào tốn CPU nhất?
- Query nào đọc nhiều nhất?
- Query nào đổi plan?
- Plan nào tốt hơn?
- Sau release query nào regression?

---

## Lab 4.1 - Top query theo duration

```sql
SELECT TOP (20)
    q.query_id,
    p.plan_id,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_logical_io_reads,
    rs.count_executions,
    qt.query_sql_text
FROM sys.query_store_runtime_stats AS rs
JOIN sys.query_store_plan AS p
    ON rs.plan_id = p.plan_id
JOIN sys.query_store_query AS q
    ON p.query_id = q.query_id
JOIN sys.query_store_query_text AS qt
    ON q.query_text_id = qt.query_text_id
ORDER BY rs.avg_duration DESC;
GO
```

---

## Lab 4.2 - Query có nhiều plan

```sql
SELECT
    q.query_id,
    COUNT(DISTINCT p.plan_id) AS plan_count,
    SUM(rs.count_executions) AS total_executions,
    MIN(rs.avg_duration) / 1000.0 AS min_avg_duration_ms,
    MAX(rs.avg_duration) / 1000.0 AS max_avg_duration_ms,
    LEFT(MAX(qt.query_sql_text), 4000) AS sample_text
FROM sys.query_store_query AS q
JOIN sys.query_store_query_text AS qt
    ON q.query_text_id = qt.query_text_id
JOIN sys.query_store_plan AS p
    ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats AS rs
    ON p.plan_id = rs.plan_id
GROUP BY q.query_id
HAVING COUNT(DISTINCT p.plan_id) > 1
ORDER BY max_avg_duration_ms DESC;
GO
```

---

## Lab 4.3 - Mô phỏng parameter sniffing

Tạo index hỗ trợ query:

```sql
CREATE INDEX IX_Orders_OrderStatus_CreatedAt
ON dbo.Orders (OrderStatus, CreatedAt)
INCLUDE (CustomerId, TotalAmount);
GO
```

Chạy với parameter phổ biến và hiếm:

```sql
EXEC dbo.SearchOrdersByStatus
    @OrderStatus = 'PAID',
    @FromDate = '2020-01-01',
    @ToDate = '2030-01-01';
GO

EXEC dbo.SearchOrdersByStatus
    @OrderStatus = 'CANCELLED',
    @FromDate = DATEADD(HOUR, -1, SYSUTCDATETIME()),
    @ToDate = SYSUTCDATETIME();
GO
```

Quan sát:

- Plan có giống nhau không?
- Estimated rows có lệch actual rows không?
- Query Store ghi mấy plan?
- Duration có biến động mạnh không?

---

## Bài học 4.2 - Các hướng xử lý parameter sniffing

Không có một cách đúng cho mọi trường hợp.

| Cách | Khi phù hợp | Rủi ro |
|---|---|---|
| Update statistics | Stats cũ hoặc sampling xấu | Có thể tốn tài nguyên |
| Index tốt hơn | Predicate rõ, workload ổn định | Tăng write cost |
| OPTION RECOMPILE | Query ít chạy, parameter rất lệch | Tăng compile CPU |
| OPTIMIZE FOR | Biết parameter đại diện | Sai nếu phân bố đổi |
| Dynamic SQL | Predicate linh hoạt | Code phức tạp |
| Force plan | Cần mitigation nhanh | Plan bị force có thể lỗi thời |

---

## Lab 4.4 - Force plan trong Query Store

Chỉ làm trong lab.

```sql
EXEC sp_query_store_force_plan
    @query_id = 123,
    @plan_id = 456;
GO
```

Gỡ force plan:

```sql
EXEC sp_query_store_unforce_plan
    @query_id = 123,
    @plan_id = 456;
GO
```

Thay `123` và `456` bằng query_id/plan_id thật.

---

## Checklist Query Store sau release hoặc sau migration

- [ ] Top duration query có đổi không?
- [ ] Top CPU query có đổi không?
- [ ] Top logical reads query có đổi không?
- [ ] Query quan trọng có plan mới không?
- [ ] Query quan trọng có duration tăng > 2x không?
- [ ] Có forced plan nào đang fail không?
- [ ] Query Store có đang read-write không?
- [ ] Query Store có gần đầy storage không?
- [ ] Có wait stats bất thường không?
- [ ] App p95 latency có tăng không?

---

## Deliverable ngày 4

```markdown
# Day 4 Query Store Report

## Top regressed query
- query_id:
- plan_id before:
- plan_id after:
- avg duration before:
- avg duration after:
- suspected cause:
- mitigation:
- long-term fix:

## Parameter sniffing note
- Procedure/query:
- Fast parameter:
- Slow parameter:
- Evidence:
- Proposed action:
```

---

# Ngày 5 - Azure SQL Managed Instance readiness

## Mục tiêu ngày 5

Sau ngày 5, người học phải:

- Giải thích được Azure SQL Managed Instance phù hợp với migration từ SQL Server khi cần mức tương thích instance cao.
- Biết kiểm tra readiness trước migration.
- Biết các nhóm vấn đề thường gặp: network, security, compatibility, jobs, logins, linked servers, cross-database dependency.
- Có assessment report cho database sẽ demo migrate.

---

## Bài học 5.1 - Vì sao chọn Azure SQL Managed Instance?

Azure SQL Managed Instance phù hợp khi:

- Ứng dụng hiện có phụ thuộc nhiều vào SQL Server instance behavior.
- Cần SQL Agent hoặc nhiều tính năng gần SQL Server hơn Azure SQL Database.
- Muốn giảm quản trị hạ tầng so với SQL Server on Azure VM.
- Migration cần ít thay đổi app hơn so với chuyển sang database-level PaaS.

Không tự động chọn Managed Instance cho mọi workload.

Nếu app hiện đại, single database, ít dependency instance-level, Azure SQL Database có thể phù hợp hơn.

Nếu cần quyền OS, custom agent, file system, third-party backup agent hoặc unsupported feature, SQL Server on Azure VM có thể phù hợp hơn.

---

## Lab 5.1 - Ghi target design

Người học cần ghi:

- Subscription.
- Resource group.
- Region.
- Managed Instance name.
- Service tier.
- vCores.
- Storage.
- Collation.
- Time zone nếu có.
- VNet/subnet.
- Private DNS/network path.
- Admin login hoặc Entra admin.

Mẫu:

```markdown
# Target Managed Instance Design

| Item | Value |
|---|---|
| Subscription | |
| Resource group | |
| Region | |
| MI name | |
| Tier | |
| vCores | |
| Storage | |
| VNet/subnet | |
| Connectivity from app | |
| Authentication | |
```

---

## Lab 5.2 - Kiểm tra source compatibility level

```sql
SELECT
    name,
    compatibility_level,
    collation_name,
    recovery_model_desc,
    containment_desc
FROM sys.databases
WHERE name = 'SalesAppDB';
GO
```

Nếu database đang SQL Server 2019, compatibility thường là 150.

Managed Instance có thể hỗ trợ nhiều compatibility level, nhưng cần ghi rõ trước/sau migration.

---

## Lab 5.3 - Kiểm tra dependency trong database

```sql
USE SalesAppDB;
GO

SELECT
    referencing_schema_name = OBJECT_SCHEMA_NAME(referencing_id),
    referencing_entity_name = OBJECT_NAME(referencing_id),
    referenced_server_name,
    referenced_database_name,
    referenced_schema_name,
    referenced_entity_name
FROM sys.sql_expression_dependencies
WHERE referenced_server_name IS NOT NULL
   OR referenced_database_name IS NOT NULL
ORDER BY referencing_schema_name, referencing_entity_name;
GO
```

Nếu có referenced server/database:

- Có linked server không?
- Có cross-database query không?
- Target Managed Instance có database liên quan không?
- App có thể sửa dependency không?

---

## Lab 5.4 - Kiểm tra SQL Agent jobs

Chạy trên source instance:

```sql
SELECT
    j.name AS job_name,
    j.enabled,
    s.step_id,
    s.step_name,
    s.subsystem,
    s.command
FROM msdb.dbo.sysjobs AS j
JOIN msdb.dbo.sysjobsteps AS s
    ON j.job_id = s.job_id
ORDER BY j.name, s.step_id;
GO
```

Ghi nhận:

- Job nào thuộc database này?
- Job có dùng file path local không?
- Job có gọi PowerShell/cmdExec không?
- Job có cần migrate sang MI Agent không?
- Job có cần thay bằng Azure Automation/Elastic Jobs không?

---

## Lab 5.5 - Kiểm tra login/user mapping

```sql
USE SalesAppDB;
GO

SELECT
    dp.name AS database_principal,
    dp.type_desc,
    dp.authentication_type_desc,
    sp.name AS server_principal,
    sp.type_desc AS server_principal_type
FROM sys.database_principals AS dp
LEFT JOIN sys.server_principals AS sp
    ON dp.sid = sp.sid
WHERE dp.type IN ('S', 'U', 'G')
  AND dp.name NOT IN ('dbo', 'guest', 'INFORMATION_SCHEMA', 'sys')
ORDER BY dp.name;
GO
```

Kiểm tra orphaned users:

```sql
SELECT
    dp.name AS orphaned_user,
    dp.type_desc
FROM sys.database_principals AS dp
LEFT JOIN sys.server_principals AS sp
    ON dp.sid = sp.sid
WHERE sp.sid IS NULL
  AND dp.authentication_type_desc = 'INSTANCE'
  AND dp.name NOT IN ('dbo', 'guest', 'INFORMATION_SCHEMA', 'sys');
GO
```

---

## Lab 5.6 - Kiểm tra feature có thể gây migration warning

```sql
USE SalesAppDB;
GO

SELECT 'CLR assemblies' AS check_name, COUNT(*) AS count_value
FROM sys.assemblies
WHERE is_user_defined = 1
UNION ALL
SELECT 'Service Broker queues', COUNT(*)
FROM sys.service_queues
WHERE is_ms_shipped = 0
UNION ALL
SELECT 'Database triggers', COUNT(*)
FROM sys.triggers
UNION ALL
SELECT 'Synonyms', COUNT(*)
FROM sys.synonyms;
GO
```

Không phải mục nào cũng là blocker. Mục tiêu là biết có gì cần assessment.

---

## Lab 5.7 - Network checklist từ Java app tới Managed Instance

Kiểm tra:

- App chạy trong cùng VNet hay peered VNet?
- DNS resolve được MI private endpoint/name không?
- Port SQL reachable không?
- NSG/route table có chặn không?
- Connection string có encrypt/trustServerCertificate phù hợp không?
- Login/auth method có hoạt động không?
- Timeout có được cấu hình hợp lý không?

Connection string mẫu:

```properties
spring.datasource.url=jdbc:sqlserver://my-mi.public-or-private-name.database.windows.net:1433;databaseName=SalesAppDB;encrypt=true;trustServerCertificate=false;loginTimeout=30;
```

Trong thực tế, hostname và security settings phải theo chuẩn tổ chức.

---

## Assessment report mẫu

```markdown
# Migration Assessment Report

## Source
- SQL Server:
- Database:
- Size:
- Compatibility:
- Recovery model:

## Target
- Azure SQL Managed Instance:
- Tier/vCores/storage:
- Network path:

## Findings
| Severity | Finding | Evidence | Owner | Action | Status |
|---|---|---|---|---|---|
| Blocker | | | | | |
| Warning | | | | | |
| Info | | | | | |

## Security
- Logins:
- Users:
- Orphaned users:
- Permissions:

## Jobs and dependencies
- SQL Agent jobs:
- Linked servers:
- Cross-database queries:
- External file paths:

## Go/No-Go
- Decision:
- Conditions:
```

---

## Checklist cuối ngày 5

- [ ] Có target Managed Instance design.
- [ ] Kết nối admin tới Managed Instance thành công.
- [ ] Kết nối app network path đã được kiểm tra hoặc có plan kiểm tra.
- [ ] Có assessment report.
- [ ] Có danh sách blocker/warning.
- [ ] Có remediation owner.
- [ ] Có quyết định migration path cho ngày 6.

---

# Ngày 6 - Migration rehearsal và validation

## Mục tiêu ngày 6

Sau ngày 6, người học phải:

- Chạy thử migration end-to-end.
- Validate schema, data, security và app.
- Có run sheet đủ chi tiết để lặp lại.
- Biết điểm nào phải rollback hoặc no-go.

---

## Bài học 6.1 - Migration không phải chỉ là copy database

Một migration được coi là có kiểm chứng khi có:

- Assessment trước migration.
- Run sheet từng bước.
- Backup hoặc restore point.
- Migration execution.
- Data validation.
- Schema validation.
- Security validation.
- App smoke test.
- Performance comparison.
- Monitoring sau cutover.
- Rollback plan.

---

## Chọn migration mode

| Mode | Khi phù hợp | Ưu điểm | Nhược điểm |
|---|---|---|---|
| Offline | Lab, downtime chấp nhận được, database nhỏ/vừa | Đơn giản hơn | Downtime dài hơn |
| Online | Cần giảm downtime | Cutover nhanh hơn | Phức tạp hơn, cần sync và monitor |

Với khóa học 7 ngày, demo có thể dùng offline migration nếu môi trường hạn chế. Tuy nhiên người học phải giải thích được online migration sẽ khác gì trong production.

---

## Run sheet rehearsal mẫu

```markdown
# Migration Rehearsal Run Sheet

## Metadata
- Source:
- Target:
- Database:
- Date:
- Operator:
- Reviewer:

## Pre-check
| Step | Command/Action | Expected | Actual | Status |
|---|---|---|---|---|
| 1 | Confirm backup | Latest full backup exists | | |
| 2 | Confirm assessment | No blocker | | |
| 3 | Confirm target connection | Login succeeds | | |
| 4 | Confirm app freeze | No writes during offline migration | | |

## Migration
| Step | Action | Start | End | Status | Notes |
|---|---|---|---|---|---|
| 1 | Start migration | | | | |
| 2 | Monitor progress | | | | |
| 3 | Confirm database online | | | | |

## Validation
| Step | Validation | Expected | Actual | Status |
|---|---|---|---|---|
| 1 | Row count | Match source | | |
| 2 | Schema check | Match source | | |
| 3 | User check | App user works | | |
| 4 | App smoke test | APIs pass | | |
| 5 | Top query check | No severe regression | | |

## Rollback
- Trigger:
- Action:
- Owner:
- Expected time:
```

---

## Lab 6.1 - Pre-migration backup

Trên source:

```sql
BACKUP DATABASE SalesAppDB
TO DISK = 'C:\Backup\SalesAppDB_PreMigration.bak'
WITH INIT, COMPRESSION, CHECKSUM, STATS = 10;
GO

RESTORE VERIFYONLY
FROM DISK = 'C:\Backup\SalesAppDB_PreMigration.bak'
WITH CHECKSUM;
GO
```

Trong môi trường không có disk path này, dùng backup location theo chuẩn lab.

---

## Lab 6.2 - Row count baseline trước migration

```sql
USE SalesAppDB;
GO

SELECT 'dbo.Customers' AS table_name, COUNT_BIG(*) AS row_count FROM dbo.Customers
UNION ALL
SELECT 'dbo.Orders', COUNT_BIG(*) FROM dbo.Orders
UNION ALL
SELECT 'dbo.OrderItems', COUNT_BIG(*) FROM dbo.OrderItems
UNION ALL
SELECT 'dbo.PaymentEvents', COUNT_BIG(*) FROM dbo.PaymentEvents;
GO
```

Lưu kết quả vào report.

---

## Lab 6.3 - Schema object count baseline

```sql
SELECT
    type_desc,
    COUNT(*) AS object_count
FROM sys.objects
WHERE is_ms_shipped = 0
GROUP BY type_desc
ORDER BY type_desc;
GO
```

---

## Lab 6.4 - Permission baseline

```sql
USE SalesAppDB;
GO

SELECT
    pr.name AS principal_name,
    pr.type_desc AS principal_type,
    pe.state_desc,
    pe.permission_name,
    OBJECT_SCHEMA_NAME(pe.major_id) AS schema_name,
    OBJECT_NAME(pe.major_id) AS object_name
FROM sys.database_permissions AS pe
JOIN sys.database_principals AS pr
    ON pe.grantee_principal_id = pr.principal_id
WHERE pr.name NOT IN ('public')
ORDER BY pr.name, pe.permission_name;
GO
```

---

## Lab 6.5 - Chạy migration rehearsal

Tùy môi trường, chọn một trong các đường:

### Đường A - Azure Database Migration Service

1. Tạo hoặc chọn DMS instance.
2. Chọn source SQL Server.
3. Chọn target Azure SQL Managed Instance.
4. Chạy assessment nếu workflow hỗ trợ.
5. Chọn database `SalesAppDB`.
6. Chạy migration.
7. Theo dõi progress.
8. Ghi lỗi nếu có.
9. Xác nhận database xuất hiện trên target.

### Đường B - Native backup/restore vào Managed Instance

1. Backup database source.
2. Upload backup lên Azure Blob Storage nếu cần.
3. Tạo credential trên Managed Instance nếu workflow yêu cầu.
4. Restore database trên Managed Instance.
5. Theo dõi restore progress.
6. Xác nhận database online.

### Đường C - BACPAC hoặc tool khác

Chỉ dùng cho lab khi dữ liệu nhỏ và không cần mô phỏng production đầy đủ. Người học phải ghi rõ giới hạn.

---

## Lab 6.6 - Validation trên target

Chạy trên Managed Instance target:

```sql
USE SalesAppDB;
GO

SELECT 'dbo.Customers' AS table_name, COUNT_BIG(*) AS row_count FROM dbo.Customers
UNION ALL
SELECT 'dbo.Orders', COUNT_BIG(*) FROM dbo.Orders
UNION ALL
SELECT 'dbo.OrderItems', COUNT_BIG(*) FROM dbo.OrderItems
UNION ALL
SELECT 'dbo.PaymentEvents', COUNT_BIG(*) FROM dbo.PaymentEvents;
GO
```

So sánh với source.

---

## Lab 6.7 - Smoke test stored procedures

```sql
EXEC dbo.GetCustomerOrders @CustomerId = 12345;
GO

EXEC dbo.SearchOrdersByStatus
    @OrderStatus = 'PAID',
    @FromDate = '2020-01-01',
    @ToDate = '2030-01-01';
GO

BEGIN TRAN;
    EXEC dbo.MarkOrderPaid @OrderId = 100, @ProviderRef = 'LAB-TEST';
ROLLBACK;
GO
```

Với stored procedure ghi dữ liệu, dùng transaction rollback trong smoke test nếu phù hợp.

---

## Lab 6.8 - Chuyển app Java sang target

Thay connection string sang Managed Instance target trong môi trường lab.

Kiểm tra:

- App start thành công.
- Connection pool tạo connection được.
- API đọc customer/orders chạy được.
- API ghi payment chạy được trong lab.
- Error log không có login timeout.
- Hikari pending connections không tăng.

---

## Lab 6.9 - So sánh performance cơ bản

Trên source và target, chạy cùng query:

```sql
SET STATISTICS IO ON;
SET STATISTICS TIME ON;

EXEC dbo.GetCustomerOrders @CustomerId = 12345;

SET STATISTICS IO OFF;
SET STATISTICS TIME OFF;
GO
```

Bảng so sánh:

| Query | Source reads | Target reads | Source elapsed | Target elapsed | Nhận xét |
|---|---:|---:|---:|---:|---|
| GetCustomerOrders | | | | | |
| SearchOrdersByStatus | | | | | |

Nếu target chậm hơn:

- Kiểm tra plan có khác không.
- Kiểm tra statistics.
- Kiểm tra compatibility level.
- Kiểm tra resource tier.
- Kiểm tra network latency từ app.
- Kiểm tra cold cache.

---

## Validation report mẫu

```markdown
# Migration Validation Report

## Migration summary
- Source:
- Target:
- Database:
- Migration mode:
- Start:
- End:

## Data validation
| Table | Source count | Target count | Status |
|---|---:|---:|---|

## Schema validation
| Object type | Source count | Target count | Status |
|---|---:|---:|---|

## Security validation
| Principal | Source | Target | Status |
|---|---|---|---|

## App smoke test
| API | Expected | Actual | Status |
|---|---|---|---|

## Performance smoke test
| Query | Source | Target | Status |
|---|---|---|---|

## Issues
| Severity | Issue | Action | Owner | Status |
|---|---|---|---|---|
```

---

## Checklist cuối ngày 6

- [ ] Rehearsal migration chạy xong.
- [ ] Có run sheet cập nhật từ thực tế.
- [ ] Có validation report.
- [ ] App Java kết nối target thành công.
- [ ] Có performance comparison.
- [ ] Có rollback plan.
- [ ] Có danh sách việc cần sửa trước demo ngày 7.

---

# Ngày 7 - Demo migration cuối khóa, monitoring và RCA

## Mục tiêu ngày 7

Người học phải demo như một Junior Azure SQL DBA:

- Có chuẩn bị.
- Có evidence.
- Có thao tác migration.
- Có validation.
- Có monitoring.
- Có rollback.
- Có giao tiếp rõ ràng.

---

## Cấu trúc buổi demo 60-90 phút

| Phần | Thời lượng | Nội dung |
|---|---:|---|
| Context | 5 phút | Source, target, app, mục tiêu |
| Baseline | 10 phút | Query Store, DB size, top query, app metrics |
| Assessment | 10 phút | Findings, blocker, remediation |
| Migration execution | 15-25 phút | Chạy hoặc replay migration |
| Validation | 15 phút | Data, schema, security, app smoke test |
| Monitoring | 10 phút | Post-migration metrics |
| Rollback/RCA | 10 phút | Rollback plan, RCA nếu có lỗi |

---

## Demo script

```markdown
# Demo Script

## 1. Context
Database SalesAppDB đang chạy trên SQL Server 2019.
Ứng dụng Java dùng JDBC/HikariCP.
Target là Azure SQL Managed Instance.
Mục tiêu demo là migration có kiểm chứng, không chỉ restore database.

## 2. Baseline
Tôi đã bật Query Store và thu baseline.
Top query theo duration là...
Database size là...
Connection pool baseline là...

## 3. Assessment
Assessment cho thấy...
Blocker đã xử lý...
Warning còn lại...
Go/No-Go là...

## 4. Migration
Tôi chạy theo run sheet...
Checkpoint hiện tại là...
Nếu lỗi ở bước này, rollback là...

## 5. Validation
Row count source và target...
Stored procedure smoke test...
App API smoke test...

## 6. Monitoring
Sau migration, tôi kiểm tra...
Không thấy blocking/deadlock...
Top query không regression nghiêm trọng...

## 7. Conclusion
Migration status:
Known issues:
Next actions:
```

---

## Post-migration monitoring queries

### Active requests

```sql
SELECT
    r.session_id,
    r.status,
    r.command,
    r.cpu_time,
    r.total_elapsed_time,
    r.logical_reads,
    r.wait_type,
    r.blocking_session_id,
    DB_NAME(r.database_id) AS database_name,
    t.text AS sql_text
FROM sys.dm_exec_requests AS r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) AS t
WHERE r.session_id <> @@SPID
ORDER BY r.total_elapsed_time DESC;
GO
```

### Top waits hiện tại

```sql
SELECT TOP (20)
    wait_type,
    waiting_tasks_count,
    wait_time_ms,
    max_wait_time_ms,
    signal_wait_time_ms
FROM sys.dm_os_wait_stats
WHERE wait_type NOT LIKE 'SLEEP%'
ORDER BY wait_time_ms DESC;
GO
```

### Query Store sau migration

```sql
SELECT TOP (20)
    qt.query_sql_text,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_logical_io_reads,
    rs.count_executions
FROM sys.query_store_runtime_stats AS rs
JOIN sys.query_store_plan AS p
    ON rs.plan_id = p.plan_id
JOIN sys.query_store_query AS q
    ON p.query_id = q.query_id
JOIN sys.query_store_query_text AS qt
    ON q.query_text_id = qt.query_text_id
ORDER BY rs.avg_duration DESC;
GO
```

### Blocking check

```sql
SELECT
    session_id,
    blocking_session_id,
    wait_type,
    wait_time,
    status,
    command
FROM sys.dm_exec_requests
WHERE blocking_session_id <> 0;
GO
```

---

## RCA mẫu nếu demo có lỗi

```markdown
# Incident RCA - Migration Demo

## Summary
Trong quá trình demo migration, lỗi xảy ra tại bước...

## Timeline
| Time | Event |
|---|---|
| 09:00 | Start migration |
| 09:10 | Error observed |
| 09:15 | Mitigation started |
| 09:25 | Service restored |

## Impact
- App/API bị ảnh hưởng:
- User impact:
- Duration:

## Root Cause
Nguyên nhân gốc là...

## Evidence
- Error message:
- Query/log:
- Metric:

## Corrective Action
- Đã làm:

## Preventive Action
- Cần thêm vào checklist:
- Cần thêm alert:
- Cần sửa run sheet:

## Lessons Learned
- 
```

---

## Rubric chấm cuối khóa

| Năng lực | 1 - Chưa đạt | 2 - Cơ bản | 3 - Đạt | 4 - Tốt |
|---|---|---|---|---|
| Baseline | Không có số liệu | Có vài số liệu rời rạc | Có baseline SQL và app | Baseline rõ, có nhận xét rủi ro |
| Execution plan | Không đọc được plan | Nhận ra scan/seek | Giải thích reads/operator | Đề xuất fix có trade-off |
| Blocking/deadlock | Không tìm được blocker | Tìm được blocker | Có root cause | Liên hệ được với Java transaction |
| Query Store | Không dùng được | Xem top query | Tìm plan/regression | Có mitigation hợp lý |
| Azure MI | Không phân biệt target | Biết MI là PaaS | Giải thích network/security | Chọn target có lý do |
| Assessment | Không có | Có checklist sơ sài | Có blocker/warning/action | Có go/no-go rõ |
| Migration | Chỉ restore/copy | Có migration steps | Có run sheet và validation | Có rollback và monitoring |
| App smoke test | Không test app | Test thủ công một API | Test read/write APIs | Có metric connection pool |
| RCA | Không viết | Viết mô tả lỗi | Có timeline/root cause/action | Có preventive action tốt |

---

## Checklist tổng hợp cho người học

### Khi query chậm

- [ ] Query nào chậm?
- [ ] Chậm từ khi nào?
- [ ] Chạy bao nhiêu lần?
- [ ] Avg duration là bao nhiêu?
- [ ] Logical reads là bao nhiêu?
- [ ] CPU time là bao nhiêu?
- [ ] Elapsed time là bao nhiêu?
- [ ] Có blocking không?
- [ ] Wait type là gì?
- [ ] Execution plan có scan lớn không?
- [ ] Có key lookup nhiều không?
- [ ] Có sort/hash spill không?
- [ ] Có implicit conversion không?
- [ ] Statistics có cũ không?
- [ ] Query Store có plan cũ tốt hơn không?
- [ ] Có deployment gần đây không?
- [ ] App connection pool có pending không?

---

### Khi API timeout

- [ ] Timeout xảy ra ở API nào?
- [ ] Timeout ở app hay database?
- [ ] Hikari active connections có chạm max không?
- [ ] Hikari pending threads có tăng không?
- [ ] SQL có session đang chạy lâu không?
- [ ] Có blocking_session_id không?
- [ ] Wait type là lock, IO, CPU hay network?
- [ ] Query text là gì?
- [ ] Transaction mở bao lâu?
- [ ] Có retry storm không?
- [ ] Có release gần đây không?
- [ ] Có tăng traffic không?

---

### Khi chuẩn bị migration

- [ ] Database owner xác nhận scope.
- [ ] App owner xác nhận connection string switch.
- [ ] Baseline đã thu.
- [ ] Assessment đã chạy.
- [ ] Blocker đã xử lý.
- [ ] Target MI đã sẵn sàng.
- [ ] Network path đã test.
- [ ] Login/user plan đã có.
- [ ] SQL Agent job plan đã có.
- [ ] Backup đã có.
- [ ] Run sheet đã review.
- [ ] Rollback plan đã review.
- [ ] Validation script đã chuẩn bị.
- [ ] Cutover window đã thống nhất.

---

### Khi sau migration

- [ ] Database online.
- [ ] Row count match.
- [ ] Schema object count match.
- [ ] App login thành công.
- [ ] App read API pass.
- [ ] App write API pass.
- [ ] Query Store ghi nhận workload.
- [ ] Top query không regression nghiêm trọng.
- [ ] Không có blocking kéo dài.
- [ ] Không có deadlock mới.
- [ ] CPU/IO trong ngưỡng chấp nhận.
- [ ] Connection pool ổn định.
- [ ] Error rate không tăng.
- [ ] Go/no-go đã ghi.

---

## Tài liệu tham chiếu trong wiki

- [[sources/master-dba-runbook-backend-java-azure-sql]]: nền tảng runbook dev-to-DBA, evidence-first, Query Store, blocking/deadlock, RCA.
- [[sources/sql-server-2022-administration-inside-out]]: nền tảng quản trị SQL Server, backup/restore, HA/DR, security, performance và cloud.
- [[sources/azure-database-migration-guides]]: quy trình migration gồm discovery, business case, assessment, conversion và migration.
- [[sources/azure-database-migration-service-documentation]]: Azure Database Migration Service, migration online/offline và target Azure SQL.
- [[concepts/azure-sql-managed-instance]]: target PaaS mức instance cho workload cần tương thích SQL Server cao.
- [[concepts/migration-assessment]]: bước đánh giá readiness, blocker, sizing và tác động.
- [[concepts/azure-database-migration-service]]: dịch vụ điều phối migration database lên Azure.

---

## Phụ lục A - Template daily standup cho khóa học

```markdown
# Daily Standup

## Hôm qua đã làm
- 

## Hôm nay sẽ làm
- 

## Evidence đã thu được
- 

## Blocker
- 

## Câu hỏi cần mentor review
- 
```

---

## Phụ lục B - Template change record cho index

````markdown
# Database Change Record

## Change
Create index:

```sql
CREATE INDEX ...
```

## Reason
- Query:
- Evidence:
- Expected benefit:

## Risk
- Write overhead:
- Storage:
- Plan change:

## Test result
| Metric | Before | After |
|---|---:|---:|
| Logical reads | | |
| CPU ms | | |
| Elapsed ms | | |

## Rollback
```sql
DROP INDEX ...
```
````

---

## Phụ lục C - Template migration go/no-go

```markdown
# Migration Go/No-Go

## Go criteria
- [ ] Assessment has no blocker
- [ ] Backup verified
- [ ] Target connection works
- [ ] App smoke test plan ready
- [ ] Rollback plan ready
- [ ] Stakeholders available

## No-go criteria
- [ ] Unresolved blocker
- [ ] Target unreachable
- [ ] Missing backup
- [ ] App owner unavailable
- [ ] Validation script not ready
- [ ] Security mapping unknown

## Decision
- Go / No-Go:
- Approver:
- Time:
- Notes:
```

---

## Phụ lục D - Mentor review questions

1. Em dựa vào evidence nào để nói query này chậm do IO?
2. Nếu tạo index này, write workload bị ảnh hưởng thế nào?
3. Nếu session đang rollback, kill thêm có giúp không?
4. Nếu Query Store có hai plan, em chọn plan nào và vì sao?
5. Nếu app timeout nhưng SQL query nhanh, em kiểm tra gì tiếp?
6. Nếu migration thành công nhưng app login fail, em kiểm tra gì?
7. Nếu row count match nhưng API sai dữ liệu, em kiểm tra gì?
8. Nếu target MI chậm hơn source, em phân biệt cold cache, resource tier và plan regression thế nào?
9. Nếu phải rollback, dữ liệu ghi mới sau cutover xử lý ra sao?
10. Nếu production cần downtime 5 phút, em chọn offline hay online migration?

---

## Phụ lục E - Kết luận

Kết thúc khóa học, người học không được chỉ nói "em đã migrate database". Người học phải chứng minh được:

- Source database đã được hiểu và đo baseline.
- Query quan trọng đã được nhận diện.
- Blocking/deadlock cơ bản có thể điều tra.
- Query Store được dùng để phát hiện regression.
- Azure SQL Managed Instance target được chọn có lý do.
- Migration có assessment và run sheet.
- Dữ liệu, security và app được validation.
- Có monitoring sau cutover.
- Có rollback plan và RCA nếu có lỗi.

Junior DBA tốt không phải người nhớ nhiều câu lệnh nhất. Junior DBA tốt là người biết thu thập đúng evidence, hành động cẩn trọng và giao tiếp rõ ràng trong lúc hệ thống đang chịu áp lực.
