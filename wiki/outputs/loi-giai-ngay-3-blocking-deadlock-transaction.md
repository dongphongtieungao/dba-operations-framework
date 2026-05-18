---
id: loi-giai-ngay-3-blocking-deadlock-transaction
title: "Lời giải ngày 3: Blocking, deadlock và transaction"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
  - concepts/sql-server-blocking-and-deadlock-investigation
  - concepts/t-sql-transactions-and-concurrency
  - concepts/incident-response-and-rca
---

# Lời giải ngày 3: Blocking, deadlock và transaction

**Tham chiếu từ runbook chính:** [[outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba]]

File này là lời giải chi tiết cho **Ngày 3 - Transaction, blocking và deadlock** trong runbook chính. Khi học hoặc chấm bài, dùng runbook chính để xác định mục tiêu, checklist và deliverable; dùng file này để xem cách làm kỹ lưỡng, evidence mẫu và lỗi thường gặp.

## Mục tiêu lời giải

Ngày 3 đạt chuẩn khi người học không chỉ tìm được session bị block, mà biết đánh giá impact, rủi ro kill session, liên hệ với app timeout và viết RCA mini.

## Ý nghĩa thực hành

Blocking và deadlock là cầu nối trực tiếp giữa code Java và vận hành database. Một transaction mở quá lâu trong app có thể làm hàng chục request khác timeout, khiến HikariCP pending tăng, rồi app team tưởng database "đơ". Nếu DBA chỉ biết nhìn query riêng lẻ mà không hiểu lock, sẽ rất dễ xử lý sai: kill nhầm session, làm rollback kéo dài, hoặc bỏ qua nguyên nhân trong transaction scope của ứng dụng.

Bài này thực dụng vì nó dạy người học cách xử lý dưới áp lực. Khi user đang bị timeout, câu hỏi không phải chỉ là "session nào block", mà là: impact bao nhiêu, blocker là ai, transaction đang làm gì, có được kill không, ai approve, rollback mất bao lâu, và sau đó sửa gì để không lặp lại. Đây là kỹ năng vận hành production cốt lõi.

Với Java developer, bài này đặc biệt quan trọng vì nhiều vấn đề lock đến từ thói quen code: mở transaction rồi gọi API ngoài, batch update quá lớn, không có index khiến update scan rộng, hoặc retry storm khi timeout. Một Junior Azure SQL DBA tốt phải nói chuyện được với app team bằng evidence, không chỉ gửi một session id.

## Tình huống thực tế tương ứng

| Tình huống | Bài thực hành giúp gì |
|---|---|
| Checkout API timeout | Correlate Hikari pending với blocking chain. |
| Job batch giữ lock lâu | Xác định head blocker và đề xuất pause/rollback/kill có approval. |
| Deadlock tăng sau release | Đọc victim, object, transaction order và đề xuất fix code/index. |
| User yêu cầu kill session | Biết hỏi transaction age, rollback risk và authority. |
| Incident P2 do lock | Viết timeline, impact, evidence và preventive action. |

## Kỹ năng thực dụng được rèn

- Tìm blocking chain và head blocker.
- Đọc wait type lock.
- Kiểm tra login, host, program và transaction age.
- Liên hệ SQL evidence với Hikari/API metric.
- Ra quyết định kill/no-kill có kiểm soát.
- Viết RCA mini có corrective và preventive action.

## Dấu hiệu làm sai bài

- Kill session ngay khi thấy `blocking_session_id`.
- Không kiểm tra blocker đang rollback hay không.
- Không thu app evidence.
- Chỉ xử lý triệu chứng, không đề xuất giảm transaction scope.
- Nhầm deadlock với blocking thường.
- Không ghi ai approve hành động rủi ro.

## Đáp án mong đợi

| Hạng mục | Đáp án đạt chuẩn |
|---|---|
| Blocking chain | Xác định blocked session, blocking session, wait type, wait time. |
| Head blocker | Biết login, host, program, transaction age, SQL text. |
| App correlation | Có Hikari pending/API timeout/error evidence. |
| Decision | Không kill khi chưa có approval và chưa hiểu rollback risk. |
| RCA | Có timeline, impact, evidence, root cause, action. |

## Bước 1 - Mô phỏng blocking

Session 1:

```sql
USE SalesAppDB;
GO

BEGIN TRAN;

UPDATE dbo.Orders
SET OrderStatus = 'PAID'
WHERE OrderId = 1;
```

Session 2:

```sql
UPDATE dbo.Orders
SET OrderStatus = 'CANCELLED'
WHERE OrderId = 1;
```

Session 3:

```sql
SELECT
    r.session_id,
    r.blocking_session_id,
    r.wait_type,
    r.wait_time / 1000.0 AS wait_seconds,
    r.status,
    r.command,
    DB_NAME(r.database_id) AS database_name,
    t.text AS sql_text
FROM sys.dm_exec_requests AS r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) AS t
WHERE r.blocking_session_id <> 0;
```

Đáp án mong đợi:

```text
Session 2 bị block bởi Session 1. Wait type thường là LCK_M_X hoặc wait lock tương tự. Root cause trực tiếp là Session 1 giữ transaction mở sau UPDATE.
```

## Bước 2 - Tìm head blocker chi tiết

```sql
SELECT
    s.session_id,
    s.login_name,
    s.host_name,
    s.program_name,
    s.status,
    s.open_transaction_count,
    s.last_request_start_time,
    s.last_request_end_time,
    t.text AS current_sql_text
FROM sys.dm_exec_sessions AS s
LEFT JOIN sys.dm_exec_requests AS r ON s.session_id = r.session_id
OUTER APPLY sys.dm_exec_sql_text(r.sql_handle) AS t
WHERE s.session_id IN
(
    SELECT DISTINCT blocking_session_id
    FROM sys.dm_exec_requests
    WHERE blocking_session_id <> 0
);
```

Cách đọc:

| Field | Ý nghĩa |
|---|---|
| `login_name` | Ai hoặc service nào đang giữ lock. |
| `host_name` | Máy/app instance liên quan. |
| `program_name` | SSMS, JDBC app, job, tool. |
| `open_transaction_count` | Có transaction mở không. |
| `last_request_start_time` | Dùng để ước lượng transaction/session age. |

## Bước 3 - Quyết định kill hay không

Đáp án đạt không được nói "kill blocker" ngay. Cần trả lời:

| Câu hỏi | Đáp án mẫu |
|---|---|
| Impact là gì? | Checkout API timeout, 18 sessions blocked, Hikari pending tăng. |
| Blocker làm gì? | UPDATE Orders trong transaction chưa commit. |
| Có đang rollback không? | Cần kiểm tra trước khi kill. |
| Kill có rủi ro gì? | Rollback transaction có thể kéo dài và tiếp tục giữ lock. |
| Ai approve? | DBA Lead hoặc Service Owner nếu production. |
| Mitigation khác? | Yêu cầu app/user commit/rollback, tạm giảm traffic, pause job. |

## Bước 4 - App correlation

Evidence app tối thiểu:

| Evidence | Kết luận |
|---|---|
| Hikari pending > 0 | Request chờ connection, có thể do DB call treo. |
| API timeout 30s | User impact rõ. |
| Error log có SQLTimeoutException | App bị ảnh hưởng ở DB call. |
| SQL blocking cùng time window | Correlation có cơ sở. |

Không đạt nếu chỉ nói "database lock nên app timeout" mà không có timestamp correlation.

## Bước 5 - RCA mini mẫu

```markdown
# Blocking RCA Mini Report

## Summary
Checkout API timeout do transaction mở giữ lock trên dbo.Orders.

## Timeline
| Time | Event |
|---|---|
| 10:00 | Lab transaction started |
| 10:02 | Second update blocked |
| 10:03 | Hikari pending increased |
| 10:05 | Blocking chain captured |
| 10:07 | Session 1 rolled back |

## Impact
- API checkout bị timeout trong lab.
- 1 update bị block trực tiếp.

## Evidence
- sys.dm_exec_requests showed blocking_session_id.
- Head blocker had open transaction.
- App metric showed pending connection increase.

## Root cause
Transaction scope quá dài, UPDATE xong nhưng không COMMIT/ROLLBACK.

## Corrective action
Rollback transaction trong lab.

## Preventive action
- App phải giữ transaction ngắn.
- Không gọi external API trong transaction.
- Alert blocking wait > threshold.
```

## Deadlock đáp án cơ bản

Nếu lab có deadlock, người học phải đọc được:

| Thành phần | Cần chỉ ra |
|---|---|
| Victim | Session bị SQL Server chọn rollback. |
| Process | Hai transaction tham gia deadlock. |
| Resource | Table/index/key/page bị tranh chấp. |
| SQL text | Câu lệnh của mỗi session. |
| Prevention | Cùng thứ tự access object, index phù hợp, transaction ngắn. |

## Lỗi thường gặp

- Kill session ngay.
- Không kiểm tra transaction age.
- Không liên hệ app metric.
- Không phân biệt blocking và deadlock.
- Không viết timeline.

## Chấm điểm gợi ý

| Hạng mục | Điểm tối đa |
|---|---:|
| Tìm blocking chain | 20 |
| Phân tích head blocker | 25 |
| App correlation | 20 |
| Decision/escalation đúng | 20 |
| RCA mini rõ | 15 |
