---
id: loi-giai-ngay-6-doc-metric-va-theo-doi-azure-sql
title: "Lời giải ngày 6: Đọc metric và theo dõi hiện trạng Azure SQL"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
  - sources/runbook-database-health-check
  - sources/runbook-capacity-report
  - concepts/azure-sql-monitoring-and-observability
  - concepts/sql-server-performance-diagnostics
  - concepts/sql-server-query-store-analysis
---

# Lời giải ngày 6: Đọc metric và theo dõi hiện trạng Azure SQL

**Tham chiếu từ runbook chính:** [[outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba]]

File này là lời giải chi tiết cho **Ngày 6 - Đọc metric và monitoring Azure SQL** trong runbook chính. Khi học hoặc chấm bài, dùng runbook chính để xác định mục tiêu, checklist và deliverable; dùng file này để xem cách làm kỹ lưỡng, evidence mẫu và lỗi thường gặp.

## Mục tiêu

Bài này bổ sung phần còn thiếu về đọc metric quan trọng và theo dõi hiện trạng Azure SQL Database/Managed Instance. Người học phải biết nhìn Azure Portal/metric, Query Store và DMV để trả lời:

- Database đang online và reachable không?
- CPU, data IO, log IO có bão hòa không?
- Sessions/workers có gần giới hạn không?
- Query nào đang gây tải?
- Có blocking/deadlock không?
- App chậm do DB hay connection pool/network?
- Có cần scale, tune query, xử lý lock hay escalation không?

## Ý nghĩa thực hành

Monitoring không phải nhìn dashboard cho đẹp. Trong production, người dùng thường báo "app chậm" hoặc alert báo CPU cao. Junior DBA phải biết đọc metric theo lớp: availability, latency, traffic, errors, saturation. Nếu chỉ nhìn CPU, người học dễ bỏ sót log IO, lock, worker/session exhaustion hoặc app pool pending.

Với Azure SQL, nhiều chỉ số quan trọng nằm trong Azure Monitor, Query Store và DMV. Bài này giúp người học kết hợp ba nguồn: metric platform, query workload và session/wait hiện tại.

## Golden signals cho database

| Golden signal | Câu hỏi | Metric/evidence |
|---|---|---|
| Availability | Database có online/reachable không? | Status, connection test, failed connections. |
| Latency | Query/transaction/API chậm không? | Query duration, API p95/p99, lock wait. |
| Traffic | Lưu lượng có tăng bất thường không? | Sessions, connections, batch requests, executions. |
| Errors | Có lỗi vận hành không? | Failed login, deadlock, timeout, backup/replication error. |
| Saturation | Tài nguyên có cạn không? | CPU, data IO, log IO, workers, sessions, storage. |

## Metric quan trọng và cách đọc

| Metric | Ý nghĩa | Khi xấu | Hành động đầu tiên |
|---|---|---|---|
| CPU percentage | Mức dùng CPU so với giới hạn tier. | Cao kéo dài, ví dụ > 80% trong nhiều phút. | Tìm top CPU query trong Query Store. |
| Data IO percentage | Mức dùng IO đọc/ghi data. | Cao kéo dài. | Tìm query reads cao, scan/sort/hash, missing index. |
| Log IO percentage | Mức ghi transaction log. | Cao khi write/bulk load/index rebuild. | Kiểm tra workload ghi, transaction lớn, index maintenance. |
| Sessions percentage | Mức dùng session. | Gần giới hạn. | Kiểm tra connection pool, app instance count. |
| Workers percentage | Mức dùng worker. | Gần giới hạn, request xếp hàng. | Kiểm tra concurrency, long-running query, parallelism. |
| Storage used | Dung lượng đã dùng. | Gần max hoặc growth nhanh. | Capacity report, cleanup/archive, scale storage. |
| Deadlock count | Số deadlock. | Spike sau release hoặc workload mới. | Capture graph, phân tích transaction order/index. |
| Failed connections/logins | Lỗi kết nối hoặc login. | Spike. | Kiểm tra firewall, auth, password, user mapping, app config. |

## Lab A - Health snapshot trong Azure Portal

Người học mở database hoặc MI trong Azure Portal và ghi:

```markdown
# Azure SQL Health Snapshot

## Scope
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Target name:
- Database:
- Time window:
- Timezone:

## Availability
- Resource status:
- Last connection test:

## Saturation
| Metric | Current/Avg | Max | Finding |
|---|---:|---:|---|
| CPU % | | | |
| Data IO % | | | |
| Log IO % | | | |
| Sessions % | | | |
| Workers % | | | |
| Storage used | | | |

## Errors
- Failed connections:
- Failed logins:
- Deadlocks:

## Initial conclusion
- Healthy / Degraded / Needs investigation:
- Evidence:
- Next action:
```

Không đạt nếu chỉ chụp screenshot mà không ghi time window và nhận xét.

### A1 - Cách đọc Azure Portal Metrics

Trong Azure Portal:

1. Mở Azure SQL Database hoặc Azure SQL Managed Instance.
2. Vào **Metrics**.
3. Chọn time range rõ ràng, ví dụ `Last 30 minutes`, `Last 1 hour` hoặc window theo ticket.
4. Chọn aggregation phù hợp: `Avg` để xem xu hướng, `Max` để bắt spike.
5. Thêm từng metric thay vì xem một biểu đồ lộn xộn.

Metric tối thiểu phải xem:

| Metric trong Portal | Cách đọc | Evidence cần ghi |
|---|---|---|
| CPU percentage | CPU có cao kéo dài hay chỉ spike. | Avg/max, time window, top query cần kiểm tra. |
| Data IO percentage | Đọc/ghi data file có bão hòa không. | Avg/max, query reads cao nếu có. |
| Log IO percentage | Ghi transaction log có nghẽn không. | Avg/max, workload ghi/job liên quan. |
| Sessions percentage | Session có gần giới hạn không. | Max, app pool size, app instance count. |
| Workers percentage | Worker có gần giới hạn không. | Max, active requests/concurrency. |
| Storage used/allocated | Dung lượng và growth. | Current value, capacity action nếu cần. |
| Deadlocks/Failed connections nếu có | Lỗi vận hành. | Count, thời điểm, correlation với app log. |

### A2 - Portal evidence không được thiếu

```markdown
## Azure Portal metric evidence
- Resource:
- Target type:
- Time range:
- Timezone:
- Aggregation: Avg/Max
- CPU avg/max:
- Data IO avg/max:
- Log IO avg/max:
- Sessions max:
- Workers max:
- Failed connections/deadlocks:
- Portal finding:
- Next query/DMV to run:
```

### A3 - Alerts và diagnostic settings

Nếu lab hoặc tổ chức có bật alert:

- Vào **Alerts** để xem alert nào fired, severity, fired time và resolved time.
- Vào **Diagnostic settings** để kiểm tra metric/log có được gửi về Log Analytics/Event Hub/Storage không.
- Không tự tạo alert production nếu chưa có chuẩn đặt tên, action group và owner.

Evidence mẫu:

```markdown
## Alert/diagnostic evidence
- Alert name:
- Severity:
- Fired time:
- Resolved time:
- Action group/owner:
- Diagnostic destination:
- Gap:
```

## Lab B - Query Store top workload

```sql
SELECT TOP (20)
    qt.query_sql_text,
    q.query_id,
    p.plan_id,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_logical_io_reads,
    rs.count_executions,
    rs.last_execution_time
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q
    ON qt.query_text_id = q.query_text_id
JOIN sys.query_store_plan AS p
    ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats AS rs
    ON p.plan_id = rs.plan_id
ORDER BY rs.avg_duration DESC;
```

Đọc kết quả:

| Dấu hiệu | Ý nghĩa |
|---|---|
| Duration cao, CPU cao | Có thể CPU-bound hoặc xử lý nhiều row. |
| Duration cao, CPU thấp | Có thể wait, lock, IO, network hoặc worker. |
| Logical reads cao | Query đọc nhiều page, cần xem plan/index. |
| Executions cao | Query nhỏ nhưng gọi quá nhiều, có thể N+1 từ app. |
| Plan_id thay đổi | Có thể regression hoặc plan variability. |

## Lab C - Active requests và blocking

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
    t.text AS sql_text
FROM sys.dm_exec_requests AS r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) AS t
WHERE r.session_id <> @@SPID
ORDER BY r.total_elapsed_time DESC;
```

Blocking riêng:

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
```

Đọc kết quả:

- `blocking_session_id <> 0`: có blocking.
- `wait_type` lock: điều tra transaction/blocker.
- `logical_reads` tăng nhanh: query đọc nhiều.
- `total_elapsed_time` cao nhưng CPU thấp: thường đang chờ.

## Lab D - Resource stats cho Azure SQL Database

Với Azure SQL Database, có thể dùng DMV database resource stats:

```sql
SELECT TOP (60)
    end_time,
    avg_cpu_percent,
    avg_data_io_percent,
    avg_log_write_percent,
    avg_memory_usage_percent,
    max_worker_percent,
    max_session_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Đọc kết quả:

| Cột | Ý nghĩa |
|---|---|
| `avg_cpu_percent` | CPU trung bình trong window. |
| `avg_data_io_percent` | Data IO so với giới hạn. |
| `avg_log_write_percent` | Log write so với giới hạn. |
| `max_worker_percent` | Worker gần giới hạn không. |
| `max_session_percent` | Session gần giới hạn không. |

Nếu CPU cao:

1. Tìm top CPU query.
2. Xem execution plan.
3. Kiểm tra logical reads.
4. Kiểm tra release/change gần nhất.
5. Chỉ scale khi có lý do hoặc để mitigation tạm.

Nếu log IO cao:

1. Kiểm tra workload ghi.
2. Kiểm tra transaction lớn.
3. Kiểm tra index maintenance/bulk load.
4. Kiểm tra retry storm từ app.

## Lab E - App correlation với HikariCP

Metric app cần đặt cạnh DB metric:

| Hikari/API metric | Nếu tăng | Cần kiểm tra |
|---|---|---|
| `hikaricp.connections.active` | Pool đang bận. | Query lâu, app giữ connection lâu. |
| `hikaricp.connections.idle = 0` | Pool căng. | Pending/timeout và DB waits. |
| `hikaricp.connections.pending > 0` | Request chờ connection. | DB latency, pool size, connection leak. |
| API p95/p99 tăng | User thấy chậm. | Query Store, waits, network. |
| DB metric bình thường nhưng app chậm | Có thể app/network/pool issue. | Thread dump, connection pool, external dependency. |

## Bài thực hành hoàn chỉnh ngày 6

### Kịch bản 1 - App báo chậm

Người học nhận một ticket: "API checkout chậm trong 30 phút gần đây". Không được kết luận ngay là database chậm; phải đọc evidence theo lớp.

```markdown
# Scenario D6-S1 - App slow investigation

## Ticket
- Symptom:
- Start time:
- Affected API/job:
- Database:
- Time window:

## First facts
- API p95/p99:
- Error rate:
- Hikari active/idle/pending:
- DB CPU/Data IO/Log IO:
- Blocking:
```

Thứ tự thực hành:

1. Xác định time window, ví dụ 30 phút gần nhất.
2. Đọc metric platform: CPU, Data IO, Log IO, sessions, workers.
3. Mở Query Store để tìm top duration và top CPU/reads.
4. Chạy active request để xem query đang chạy và wait hiện tại.
5. Kiểm tra blocking nếu elapsed cao nhưng CPU thấp.
6. Đặt app metric cạnh DB metric.
7. Kết luận `DB likely`, `App likely`, `mixed`, hoặc `insufficient evidence`.

### Kịch bản 2 - CPU cao

```markdown
# Scenario D6-S2 - High CPU

## Evidence
- CPU % max/avg:
- Time window:
- Top CPU query:
- Execution count:
- Recent release/change:
- Plan changed: yes/no/unknown

## Decision
- Immediate mitigation:
- Root cause path:
- Owner:
```

Người học phải phân biệt:

| Dấu hiệu | Cách đọc |
|---|---|
| CPU cao + một query CPU cao | Ưu tiên plan/index/query rewrite. |
| CPU cao + nhiều query nhỏ gọi rất nhiều | Có thể app gọi quá dày hoặc N+1 query. |
| CPU cao sau release | Kiểm tra query mới, plan regression, parameter change. |
| CPU cao nhưng không có top query rõ | Kiểm tra workload tổng, workers, parallelism, telemetry gap. |

Scale có thể là mitigation tạm nếu đang có incident, nhưng health report vẫn phải ghi hướng root cause.

### Kịch bản 3 - Blocking hoặc worker/session cao

```sql
SELECT
    r.session_id,
    r.blocking_session_id,
    r.wait_type,
    r.wait_time,
    r.status,
    r.command,
    r.total_elapsed_time,
    t.text AS sql_text
FROM sys.dm_exec_requests AS r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) AS t
WHERE r.blocking_session_id <> 0
ORDER BY r.wait_time DESC;
```

Nếu có blocking, người học ghi:

- Session nào bị block.
- Wait type là gì.
- Có head blocker không.
- Query/transaction liên quan.
- Có được kill session không, hay phải escalation.

Nếu workers/sessions cao nhưng blocking không rõ, kiểm tra app connection pool, retry storm, long-running query và số app instance.

### Kịch bản 4 - Daily monitoring handoff

Mẫu handoff ngắn cuối ca:

```markdown
# Daily Azure SQL Monitoring Handoff

## Window
- Time range:
- Target:
- Database:

## Status
- Overall: Healthy / Watch / Degraded / Incident
- Main risk:

## Metrics
- CPU:
- Data IO:
- Log IO:
- Sessions/workers:
- Storage:
- Failed connections/deadlocks:

## Workload
- Top query:
- Query Store finding:
- Blocking:

## App correlation
- API p95/p99:
- Hikari active/idle/pending:
- Error rate:

## Action
- Decision: no action / investigate / mitigate / escalate
- Owner:
- Next check:
```

## Decision tree thực dụng

| Triệu chứng | Khả năng cao | Bước tiếp |
|---|---|---|
| CPU cao + top query CPU cao | Query CPU-bound | Plan/index/query rewrite. |
| Data IO cao + reads cao | Query scan/IO-heavy | Execution plan, index, statistics. |
| Log IO cao | Write-heavy/transaction/index maintenance | Kiểm tra write workload. |
| Sessions/workers cao | Concurrency/pool/retry storm | App pool, active sessions, throttle. |
| Duration cao + blocking | Locking | Tìm head blocker. |
| Failed login spike | Auth/security/config | Kiểm tra credential/firewall/user. |
| App pending cao + DB bình thường | App pool leak/network/external | App-side investigation. |

## Health report mẫu

```markdown
# Azure SQL Health Report

## Scope
- Target type:
- Target name:
- Database:
- Time window:

## Summary
- Status: Healthy / Degraded / Incident
- Main finding:

## Platform metrics
| Metric | Value | Finding |
|---|---:|---|
| CPU % | | |
| Data IO % | | |
| Log IO % | | |
| Sessions % | | |
| Workers % | | |

## Query Store
| Query ID | Avg duration | Avg CPU | Reads | Finding |
|---|---:|---:|---:|---|

## Active requests/blocking
- Blocking: Yes/No
- Long-running sessions:

## App correlation
- API p95/p99:
- Hikari active/idle/pending:
- Error rate:

## Decision
- No action / investigate / mitigate / escalate:
- Owner:
- Next check time:
```

## Dấu hiệu làm sai bài

- Chỉ nhìn CPU.
- Không ghi time window.
- Không phân biệt platform metric và query metric.
- Không correlate với app.
- Không kiểm tra blocking khi elapsed cao.
- Scale tài nguyên trước khi biết query nào gây tải.
- Kết luận "DB chậm" khi DB metrics bình thường.

## Chấm điểm gợi ý

| Hạng mục | Điểm tối đa |
|---|---:|
| Health snapshot có time window | 15 |
| Đọc metric đúng ý nghĩa | 25 |
| Query Store analysis | 20 |
| Active request/blocking check | 15 |
| App correlation | 15 |
| Decision/next action | 10 |
