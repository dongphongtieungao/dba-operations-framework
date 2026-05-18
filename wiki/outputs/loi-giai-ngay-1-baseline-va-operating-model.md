---
id: loi-giai-ngay-1-baseline-va-operating-model
title: "Lời giải ngày 1: Baseline và operating model"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
  - sources/dba-operations-framework
  - concepts/dba-service-catalog
  - concepts/dba-operating-model
  - concepts/dba-evidence-driven-operations
---

# Lời giải ngày 1: Baseline và operating model

**Tham chiếu từ runbook chính:** [[outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba]]

File này là lời giải chi tiết cho **Ngày 1 - DBA mindset, service catalog và baseline** trong runbook chính. Khi học hoặc chấm bài, dùng runbook chính để xác định mục tiêu, checklist và deliverable; dùng file này để xem cách làm kỹ lưỡng, evidence mẫu và lỗi thường gặp.

## Mục tiêu lời giải

Ngày 1 đạt chuẩn khi người học không chỉ chạy được vài câu query baseline, mà biết biến baseline thành evidence có thể dùng cho migration, troubleshooting và go/no-go sau này.

## Ý nghĩa thực hành

Baseline là điểm tựa của mọi quyết định DBA. Nếu không biết trạng thái bình thường của database và app, người vận hành rất dễ nhầm lẫn giữa "hệ thống đang lỗi" và "hệ thống vốn đã như vậy". Với migration, baseline là bằng chứng để trả lời câu hỏi sau cutover: target có tốt hơn, tệ hơn hay chỉ khác do workload thay đổi.

Trong thực tế production, DBA thường bị gọi khi API chậm, migration bị nghi ngờ làm giảm hiệu năng, hoặc app owner hỏi "trước đây có bị vậy không". Nếu ngày 1 làm kỹ, người học có thể mở lại số liệu trước migration: SQL version, database option, Query Store, file size, API p95/p99, Hikari pool. Nếu không có các số liệu này, cuộc điều tra dễ trở thành tranh luận cảm tính giữa app team, DBA và infra.

Tính thực dụng của bài này nằm ở chỗ nó rèn thói quen **đóng khung vấn đề trước khi xử lý**. Junior DBA chưa cần tự thiết kế kiến trúc lớn, nhưng phải biết hỏi đúng: database nào, app nào, owner nào, metric nào, time window nào, evidence ở đâu. Đây là kỹ năng nền cho incident, change, tuning và migration.

## Tình huống thực tế tương ứng

| Tình huống | Baseline giúp gì |
|---|---|
| App chậm sau migration | So sánh API latency, Query Store reads/duration, Hikari pending trước và sau. |
| Azure target bị nghi thiếu tài nguyên | Có source size, workload và top query để đối chiếu sizing. |
| Query Store sau cutover chưa có dữ liệu | Biết đó là measurement gap, không vội kết luận target tốt/xấu. |
| App team nói "DB chậm" | DBA có thể kiểm tra DB metric và app pool thay vì nhận lỗi cảm tính. |
| CAB hỏi rủi ro migration | Có risk list và baseline dossier làm đầu vào go/no-go. |

## Kỹ năng thực dụng được rèn

- Biến yêu cầu mơ hồ thành ticket có scope.
- Thu SQL baseline đủ dùng cho troubleshooting.
- Thu app baseline để không chỉ nhìn database một chiều.
- Nhận diện measurement gap thay vì che giấu thiếu dữ liệu.
- Viết risk/action có owner.
- Không lưu secret trong evidence.

## Dấu hiệu làm sai bài

- Chỉ chạy query nhưng không biết output dùng để làm gì.
- Không ghi time window, khiến số liệu không so sánh được.
- Không có app baseline, trong khi đối tượng là Java app.
- Không ghi learning gap khi thiếu APM/Hikari metric.
- Kết luận target migration-ready chỉ từ SQL version và database option.

## Đáp án mong đợi

| Hạng mục | Đáp án đạt chuẩn |
|---|---|
| Scope | Xác định rõ app, database, source, target, owner, môi trường. |
| Architecture path | Mô tả được request từ Java API tới SQL endpoint, gồm JDBC/HikariCP và network. |
| SQL baseline | Có version, edition, compatibility level, recovery model, Query Store, file size. |
| App baseline | Có p95/p99, Hikari active/idle/pending/timeout, error rate nếu có. |
| Risk list | Có rủi ro cụ thể, evidence, owner và action. |
| Evidence quality | Không chứa secret, có timestamp/time window, đủ ngữ cảnh. |

## Quy ước target trong ngày 1

Baseline ngày 1 phải ghi rõ target type:

| Target type | Cần ghi thêm |
|---|---|
| Azure SQL Database | Logical server, database name, endpoint, firewall/private endpoint plan, contained user/auth model. |
| Azure SQL Managed Instance | MI name, database name, VNet/subnet, endpoint/FQDN, login-user mapping/auth model. |

Không đạt nếu baseline dossier chỉ ghi "Azure SQL" hoặc "cloud DB" mà không phân biệt hai loại.

## Bước 1 - Tạo ticket giả lập đúng chuẩn

Ví dụ đạt:

```markdown
# LAB-DBA-D1 - Baseline SalesAppDB before migration

## Request
Thu baseline cho SalesAppDB trước khi migration lên target đã chọn: Azure SQL Database hoặc Azure SQL Managed Instance.

## Scope
- App: SalesApp
- Database: SalesAppDB
- Source: SQL Server 2019 LAB-SQL01
- Target candidate type: Azure SQL Managed Instance
- Target candidate name: azsqlmi-lab-01
- Environment: Lab
- Owner: App team / DBA trainee

## Acceptance criteria
- Có SQL baseline.
- Có app connection pool baseline.
- Có danh sách rủi ro.
- Evidence không chứa secret.
```

Không đạt nếu chỉ ghi: "Kiểm tra database trước migration".

## Bước 2 - Thu SQL Server instance baseline

Query:

```sql
SELECT
    @@SERVERNAME AS server_name,
    @@VERSION AS sql_version,
    SERVERPROPERTY('Edition') AS edition,
    SERVERPROPERTY('ProductVersion') AS product_version,
    SERVERPROPERTY('ProductLevel') AS product_level;
```

Cách đọc:

| Output | Ý nghĩa |
|---|---|
| `server_name` | Dùng để định danh source. |
| `sql_version` | Kiểm tra version/patch; migration assessment cần biết. |
| `edition` | Một số feature phụ thuộc edition. |
| `product_version` | So sánh compatibility và support matrix. |

Kết luận mẫu:

```text
Source là SQL Server 2019 Developer/Enterprise lab, version đã ghi nhận. Chưa kết luận migration-ready; đây mới là instance baseline.
```

## Bước 3 - Thu database option baseline

Query:

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
```

Đáp án mong đợi:

| Trường | Pass | Nếu không pass |
|---|---|---|
| `compatibility_level` | Ghi nhận rõ, ví dụ 150. | Không tự đổi, tạo action item. |
| `recovery_model_desc` | `FULL` cho lab migration/backup. | Nếu `SIMPLE`, ghi rủi ro PITR/log backup. |
| `page_verify_option_desc` | `CHECKSUM`. | Đề xuất chuẩn hóa trước production. |
| `is_auto_create_stats_on` | 1. | Ghi risk về optimizer. |
| `is_auto_update_stats_on` | 1. | Ghi risk về stale statistics. |
| `is_query_store_on` | 1. | Bật trong lab hoặc ghi lý do chưa bật. |

## Bước 4 - Thu size baseline

Query:

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
```

Cách kết luận:

- Data file size dùng để ước lượng thời gian migration và target storage.
- Log file size/free space dùng để phát hiện rủi ro log growth.
- Không shrink file chỉ vì thấy free space.

## Bước 5 - Thu Query Store baseline

Nếu Query Store chưa bật trong lab:

```sql
ALTER DATABASE SalesAppDB SET QUERY_STORE = ON;
```

Sau khi có workload:

```sql
SELECT TOP (10)
    qt.query_sql_text,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_logical_io_reads AS avg_logical_reads,
    rs.count_executions,
    p.plan_id,
    q.query_id
FROM sys.query_store_runtime_stats AS rs
JOIN sys.query_store_plan AS p ON rs.plan_id = p.plan_id
JOIN sys.query_store_query AS q ON p.query_id = q.query_id
JOIN sys.query_store_query_text AS qt ON q.query_text_id = qt.query_text_id
ORDER BY rs.avg_duration DESC;
```

Nếu không có dữ liệu Query Store:

```text
Không được bỏ qua. Cần chạy workload Java hoặc stored procedure nhiều lần rồi thu lại.
```

## Bước 6 - Thu app baseline

Metric tối thiểu:

| Metric | Nguồn | Cách dùng |
|---|---|---|
| API p95/p99 | APM hoặc log | So sánh trước/sau migration. |
| Hikari active | Actuator/APM | Biết pool đang dùng bao nhiêu connection. |
| Hikari idle | Actuator/APM | Nếu bằng 0 lâu dài, pool có thể căng. |
| Hikari pending | Actuator/APM | Nếu > 0, request đang chờ connection. |
| Timeout count | Log/APM | Correlate với DB wait/lock/network. |

Nếu chưa có APM, câu trả lời đạt vẫn phải ghi:

```text
Measurement gap: chưa có APM/Hikari metrics tự động. Tạm dùng app log và cần action item để bật actuator hoặc metric exporter.
```

## Baseline dossier mẫu

```markdown
# Baseline Dossier - SalesAppDB

## Scope
- Ticket: LAB-DBA-D1
- App: SalesApp
- Database: SalesAppDB
- Source: LAB-SQL01
- Target candidate: azsqlmi-lab-01
- Target type: Azure SQL Managed Instance
- Owner: DBA trainee

## SQL baseline
| Area | Value | Finding |
|---|---|---|
| Version | SQL Server 2019 | Recorded |
| Compatibility level | 150 | OK |
| Recovery model | FULL | OK for log chain lab |
| Page verify | CHECKSUM | OK |
| Query Store | ON | OK |
| Size | Data/log recorded | Use for migration estimate |

## App baseline
| Metric | Value | Finding |
|---|---:|---|
| API p95 | 180 ms | Baseline |
| API p99 | 450 ms | Baseline |
| Hikari active | 4 | Normal |
| Hikari idle | 6 | Normal |
| Hikari pending | 0 | Normal |

## Risks
| Risk | Evidence | Action | Owner |
|---|---|---|---|
| No automated Hikari dashboard | Metrics collected manually | Add dashboard | App team |
```

## Lỗi thường gặp

- Chỉ lưu query, không lưu output.
- Không ghi time window.
- Không có app baseline nên sau migration không biết app chậm do đâu.
- Bật Query Store nhưng không chạy workload.
- Ghi connection string có password vào evidence.

## Chấm điểm gợi ý

| Hạng mục | Điểm tối đa |
|---|---:|
| Ticket và scope rõ | 15 |
| SQL baseline đủ | 25 |
| App baseline đủ | 20 |
| Risk/action có owner | 20 |
| Evidence sạch và tái kiểm tra được | 20 |
