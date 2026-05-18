---
id: loi-giai-ngay-7-readiness-migration-demo-cutover
title: "Lời giải ngày 7: Readiness, migration rehearsal, demo cutover, monitoring và RCA"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
  - concepts/azure-sql-database
  - concepts/azure-sql-managed-instance
  - concepts/private-endpoints
  - concepts/sql-server-security-auditing
  - concepts/database-migration
  - concepts/migration-assessment
  - concepts/restore-validation-runbook
  - concepts/azure-sql-monitoring-and-observability
  - concepts/incident-response-and-rca
  - concepts/dba-evidence-driven-operations
  - sources/azure-database-migration-guides
---

# Lời giải ngày 7: Readiness, migration rehearsal, demo cutover, monitoring và RCA

**Tham chiếu từ runbook chính:** [[outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba]]

File này là lời giải chi tiết cho **Ngày 7 - Readiness, migration rehearsal và demo cutover** trong runbook chính. Khi học hoặc chấm bài, dùng runbook chính để xác định mục tiêu, checklist và deliverable; dùng file này để xem cách làm kỹ lưỡng, evidence mẫu và lỗi thường gặp.

## Mục tiêu lời giải

Ngày 7 là bài tổng hợp cuối khóa. Người học phải trình bày được một ca migration như vận hành thật: chọn target rõ ràng, kiểm tra readiness, chạy migration rehearsal, validate dữ liệu/schema/security/app/performance, chuẩn bị rollback, theo dõi sau cutover và viết RCA nếu có lỗi.

Không được nói chung chung là "migrate lên Azure SQL". Mọi evidence phải ghi rõ target là **Azure SQL Database** hoặc **Azure SQL Managed Instance**. Nếu demo cả hai track, phải tách scope, run sheet và validation riêng.

## Ý nghĩa thực hành

Migration hiếm khi thất bại chỉ vì tool không copy được database. Lỗi thực tế thường nằm ở lớp readiness và validation: private endpoint DNS sai, firewall chặn, login tồn tại nhưng user chưa map, app dùng quyền admin, row count lệch do chưa freeze write, Query Store cho thấy plan regression, Hikari pending tăng, hoặc rollback không xử lý dữ liệu ghi mới sau cutover.

Ngày 7 vì vậy không phải buổi trình diễn công cụ. Đây là bài kiểm tra tư duy DBA vận hành: biết hệ thống đang ở trạng thái nào, evidence ở đâu, còn rủi ro gì, điều kiện go/no-go là gì, ai quyết định, và nếu lỗi xảy ra thì timeline/RCA/preventive action ra sao.

Junior DBA chưa nhất thiết là người ra quyết định cutover production, nhưng phải cung cấp evidence đủ rõ để DBA lead, service owner hoặc CAB quyết định.

## Chuỗi năng lực ngày 7

| Giai đoạn | Câu hỏi phải trả lời | Evidence bắt buộc |
|---|---|---|
| Target readiness | Target nào, vì sao, còn trade-off gì? | Target choice, compatibility, network, security, capacity. |
| Connectivity | App có đi đúng đường tới target không? | FQDN, DNS, firewall/private endpoint/VNet, JDBC/TLS/auth. |
| Security | App principal có đúng quyền không? | Login/user/role/permission, audit, secret handling. |
| Migration rehearsal | Migration có chạy theo run sheet không? | Start/end/status/error, tool/mode, backup/restore point. |
| Validation | Source và target có tương đương không? | Row count, object count, app smoke test, performance check. |
| Rollback | Khi nào rollback, ai quyết, rollback mất bao lâu? | Trigger, owner, steps, data reconciliation. |
| Monitoring | Sau cutover có regression không? | CPU, data IO, log IO, sessions/workers, Query Store, blocking, app metrics. |
| RCA | Nếu lỗi, tổ chức học được gì? | Timeline, impact, root cause, corrective/preventive action. |

## Track A và Track B

| Track | Target | Khi phù hợp | Điểm cần chú ý |
|---|---|---|---|
| Track A | Azure SQL Database | App dùng một database độc lập, ít phụ thuộc instance-level feature. | Logical server, firewall/private endpoint, contained user hoặc Entra principal. |
| Track B | Azure SQL Managed Instance | Cần tương thích SQL Server cao hơn, SQL Agent, cross-database hoặc instance-level surface. | MI VNet/subnet/DNS, login-user mapping, SQL Agent, instance-level config. |

Kết luận đạt chuẩn phải nêu được cả lý do chọn và lựa chọn thay thế:

```text
SalesAppDB chọn Azure SQL Managed Instance trong lab vì migration từ SQL Server 2019 có giả định cần SQL Agent và compatibility instance-level. Nếu app chỉ dùng một database độc lập, không có SQL Agent hoặc cross-database dependency, Azure SQL Database vẫn là option cần đánh giá vì đơn giản và kinh tế hơn.
```

## Phần 1 - Readiness target

### 1.1. Compatibility assessment

Assessment không được chỉ ghi "pass". Cần có bảng finding:

| Area | Ví dụ finding | Severity | Action | Owner |
|---|---|---|---|---|
| SQL Agent jobs | Có job gửi report hằng đêm | Medium | Track A: thay bằng Azure Automation/Elastic Jobs; Track B: validate SQL Agent trên MI | DBA |
| Linked server | Có linked server tới legacy DB | High | Kiểm tra support/network hoặc loại bỏ | DBA/App |
| Cross-database query | App query database khác | Medium | Track B thường phù hợp hơn; Track A cần thiết kế lại | App |
| CLR/FileStream/xp_cmdshell | Có feature rủi ro | High | Remediate hoặc chọn SQL VM | DBA Lead |
| Compatibility level | 150 | Low | Ghi nhận và test plan | DBA |

No-go nếu có blocker chưa có action owner.

### 1.2. Network readiness

Checklist đạt chuẩn:

- Endpoint target đúng: logical server FQDN hoặc MI FQDN.
- Track A: firewall rule hoặc private endpoint logical server đúng.
- Track B: app subnet/VNet/peering/VPN/private endpoint path đúng.
- DNS resolve về IP mong muốn.
- NSG/firewall không chặn port SQL.
- Không mở firewall quá rộng chỉ để test nhanh mà không ghi risk.

Azure Portal checklist:

| Portal area | Track A - Azure SQL Database | Track B - Managed Instance | Evidence |
|---|---|---|---|
| Overview | Logical server/database FQDN và status. | MI FQDN và status. | Resource name, status, FQDN. |
| Networking | Firewall, public access, private endpoint connections. | VNet/subnet, private/public endpoint, NSG/route liên quan. | Expected path và finding. |
| Private endpoint connections | Approved/rejected/pending state. | Approved/rejected/pending state nếu dùng. | Connection state và private DNS note. |
| Connection strings | JDBC endpoint pattern. | JDBC endpoint pattern. | Endpoint pattern, không ghi secret. |
| Activity log | Network/security changes gần đây. | Network/security changes gần đây. | Change time, caller nếu được phép xem. |

Evidence mẫu:

```markdown
## Network evidence
- Target type:
- FQDN:
- Expected path: public firewall / private endpoint / MI VNet
- DNS result:
- App subnet:
- Firewall/NSG finding:
- Test result:
```

### 1.3. Java connectivity troubleshooting

Thứ tự kiểm tra đúng:

1. FQDN, port, database name.
2. DNS resolve.
3. Network/firewall/private endpoint/NSG.
4. TLS/JDBC driver.
5. Authentication.
6. Login/user mapping.
7. Permission.
8. Hikari pool.
9. Azure session/worker/resource saturation.

| Lỗi | Giả thuyết ưu tiên |
|---|---|
| `Client with IP address ... is not allowed` | Azure SQL Database firewall/public endpoint rule. |
| Timeout khi mở socket | DNS, private endpoint, VNet route, NSG, firewall. |
| Login failed for user | Credential, auth mode, login. |
| Cannot open database requested by login | Database name, contained user, default database, login-user mapping. |
| TLS/certificate error | JDBC driver, encrypt/trust certificate config. |
| Hikari pending tăng | Pool exhaustion, DB slow, connection creation slow hoặc session limit. |

### 1.4. Security readiness

| Control | Cần có |
|---|---|
| App principal | Service account/managed identity, không dùng admin. |
| Least privilege | Role/quyền đúng phạm vi. |
| Privileged access | Approval, expiry, evidence. |
| TDE | Bật hoặc ghi trạng thái platform. |
| Audit | Login/permission/schema/privileged operation nếu policy yêu cầu. |
| Defender/VA | Review nếu tổ chức dùng. |
| Secret handling | Không ghi secret vào runbook/evidence. |

Azure Portal security evidence:

```markdown
## Portal security evidence
- Microsoft Entra/admin setting:
- SQL authentication setting if visible/applicable:
- Defender/auditing status if used by org:
- Firewall/private endpoint status:
- Recent Activity log security/network changes:
- Secret exposed in evidence: no
```

Query kiểm tra permission:

```sql
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
```

Với Managed Instance, kiểm tra thêm login-user mapping:

```sql
SELECT
    dp.name AS database_user,
    dp.type_desc,
    dp.authentication_type_desc,
    dp.default_schema_name
FROM sys.database_principals AS dp
WHERE dp.name NOT IN ('dbo', 'guest', 'INFORMATION_SCHEMA', 'sys')
ORDER BY dp.name;
```

### 1.5. Capacity readiness

Người học phải so sánh:

- Source database size và growth trend.
- Top query reads/CPU/duration.
- Concurrent sessions.
- Hikari maximum pool x số app instances.
- Target tier vCore/storage/IO.
- Metric cần monitor sau cutover: CPU, data IO, log IO, workers, sessions.

Kết luận đạt:

```text
Target tier đủ cho rehearsal nhưng chưa kết luận production-ready vì chưa có workload test với concurrency tương đương production. Cần load test hoặc pilot monitoring.
```

## Phần 2 - Migration rehearsal

### 2.1. Pre-migration backup hoặc restore point

Với SQL Server source:

```sql
BACKUP DATABASE SalesAppDB
TO DISK = 'C:\Backup\SalesAppDB_PreMigration.bak'
WITH INIT, COMPRESSION, CHECKSUM, STATS = 10;

RESTORE VERIFYONLY
FROM DISK = 'C:\Backup\SalesAppDB_PreMigration.bak'
WITH CHECKSUM;
```

Đáp án đạt:

```text
Backup completed and VERIFYONLY passed. Evidence includes backup time, file/location, checksum result. For production, backup location and retention must follow policy.
```

Không đạt nếu chỉ ghi "đã backup".

### 2.2. Source baseline

```sql
USE SalesAppDB;
GO

SELECT 'dbo.Customers' AS table_name, COUNT_BIG(*) AS row_count FROM dbo.Customers
UNION ALL
SELECT 'dbo.Orders', COUNT_BIG(*) FROM dbo.Orders
UNION ALL
SELECT 'dbo.PaymentEvents', COUNT_BIG(*) FROM dbo.PaymentEvents;

SELECT
    type_desc,
    COUNT(*) AS object_count
FROM sys.objects
WHERE is_ms_shipped = 0
GROUP BY type_desc
ORDER BY type_desc;
```

Cần lưu output source trước khi migrate, kèm thời gian và môi trường.

### 2.3. Chọn migration mode

| Mode | Khi dùng | Ghi chú |
|---|---|---|
| Offline | Lab, downtime chấp nhận được. | Đơn giản hơn, dễ kiểm soát validation. |
| Online | Production downtime thấp. | Cần sync monitoring, cutover window và rollback phức tạp hơn. |
| Backup/restore vào MI | Track B. | Phù hợp SQL Server to Managed Instance khi backup path được hỗ trợ. |
| BACPAC/DACPAC | Track A lab/small DB. | Ghi hạn chế về size, downtime và feature. |
| DMS | Tùy source/target/mode được hỗ trợ. | Phải ghi rõ track và mode. |

Kết luận đạt:

```text
Lab dùng offline migration để tập validation. Production online migration cần thêm sync monitoring, cutover window, rollback trigger và data reconciliation.
```

### 2.4. Migration run sheet

```markdown
# Migration Run Sheet

## Scope
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Source:
- Target:
- Database:
- Migration mode:
- Tool:
- Operator:
- Decision owner:

## Pre-check
| Check | Status | Evidence |
|---|---|---|
| Backup/restore point verified | | |
| Assessment reviewed | | |
| App owner available | | |
| Write freeze or lab equivalent | | |
| Rollback owner confirmed | | |

## Execution
| Step | Start | End | Status | Evidence/Error |
|---|---|---|---|---|
| Start migration | | | | |
| Target online | | | | |
| Data validation | | | | |
| Security validation | | | | |
| App smoke test | | | | |
| Monitoring window | | | | |

## Decision
- Go/No-Go:
- Conditions:
- Known issues:
- Next action:
```

## Phần 3 - Validation

### 3.1. Target validation matrix

| Validation | Source | Target | Status | Cách quyết định |
|---|---|---|---|---|
| Row count Customers | 50000 | 50000 | Pass | Match. |
| Row count Orders | 300000 | 300000 | Pass | Match. |
| Object count PROC | 2 | 2 | Pass | Match. |
| App login | Pass source | Pass target | Pass | App principal works. |
| Read API | Pass | Pass | Pass | Smoke test. |
| Write API | Pass | Pass/rollback test | Pass | Transaction rollback test OK. |
| Top query | 20 ms | 25 ms | Pass | No severe regression. |

Nếu row count lệch:

- Không được go.
- Kiểm tra write freeze.
- Kiểm tra migration tool error.
- Kiểm tra table filter.
- Kiểm tra transaction đang ghi trong lúc migration.

### 3.2. Validation khác nhau theo target

| Validation | Azure SQL Database | Azure SQL Managed Instance |
|---|---|---|
| App identity | Contained user/Entra principal trong database. | Login ở instance + user mapping trong database hoặc Entra. |
| SQL Agent jobs | Cần thay thế/thiết kế lại nếu có. | Validate job trên MI nếu migrate. |
| Cross-database dependency | Cần đánh giá lại. | Có thể phù hợp hơn nhưng vẫn phải test. |
| Network | Firewall/private endpoint logical server. | VNet/subnet/private/public endpoint MI. |

### 3.3. App smoke test

Tối thiểu:

- App start thành công.
- Hikari pool tạo connection.
- Read API pass.
- Write API pass hoặc rollback smoke test pass.
- Error log không có login timeout/permission denied.
- Hikari pending không tăng bất thường.

## Phần 4 - Rollback plan

Rollback plan đạt phải có:

| Thành phần | Nội dung |
|---|---|
| Trigger | Validation fail, app smoke test fail, severe performance regression, security fail. |
| Decision owner | Service owner hoặc migration lead. |
| Technical steps | Revert connection string/DNS, keep source writable, stop target writes. |
| Data reconciliation | Nếu target đã nhận write sau cutover, cần plan xử lý. |
| Expected RTO | Thời gian rollback dự kiến. |
| Communication | Ai thông báo, kênh nào. |

No-go hoặc rollback nếu:

- Row count/object count lệch không giải thích được.
- App không login được.
- API chính fail.
- Query trọng yếu regression nghiêm trọng.
- Blocking/deadlock spike.
- Hikari pending/timeout tăng kéo dài.
- Target unreachable hoặc resource saturation.

## Phần 5 - Demo cutover

### 5.1. Cấu trúc demo 60-90 phút

| Phần | Thời lượng | Nội dung |
|---|---:|---|
| Context | 5 phút | Source, target, app, scope, risk. |
| Baseline | 10 phút | Version, DB size, top query, app metrics. |
| Assessment | 10 phút | Blocker, warning, remediation, go/no-go. |
| Migration execution | 15-25 phút | Chạy hoặc replay theo run sheet. |
| Validation | 15 phút | Data, schema, security, app smoke test. |
| Monitoring | 10 phút | CPU/IO/session/query/blocking/app metric. |
| Decision | 10 phút | Go/no-go, rollback trigger, known issues, next action. |

### 5.2. Demo script mẫu đạt chuẩn

```markdown
# Demo Script

## 1. Context
SalesAppDB đang chạy trên SQL Server 2019. Ứng dụng SalesApp dùng JDBC/HikariCP.
Target demo là Azure SQL Database hoặc Azure SQL Managed Instance, ghi rõ track.
Mục tiêu là migration có kiểm chứng, không chỉ copy database.

## 2. Baseline
- SQL version, compatibility, recovery model.
- Database size.
- Query Store top queries.
- Hikari active/idle/pending.
- API p95/p99.

## 3. Assessment
- Blocker:
- Warning:
- Go condition:

## 4. Execution
- Run sheet:
- Start/end:
- Error nếu có:

## 5. Validation
- Row count:
- Object count:
- App login:
- Read API:
- Write API hoặc rollback test:
- Top query:

## 6. Monitoring
- Active requests:
- Blocking:
- Query Store:
- CPU/data IO/log IO:
- Hikari pending/error rate:

## 7. Decision
- Demo status:
- Production readiness:
- Known issues:
- Next action:
```

Nếu demo Track A:

```markdown
Target demo là Azure SQL Database trên logical server <server>.database.windows.net.
Migration mode dùng cho lab là BACPAC/DACPAC hoặc DMS tùy môi trường.
Validation tập trung vào database-level object, contained user/app auth, firewall/private endpoint và app smoke test.
```

Nếu demo Track B:

```markdown
Target demo là Azure SQL Managed Instance <mi-fqdn>.
Migration mode dùng cho lab là native backup/restore vào MI hoặc DMS tùy môi trường.
Validation tập trung thêm vào login-user mapping, SQL Agent/cross-database dependency nếu có, VNet/subnet/DNS và MI metrics.
```

## Phần 6 - Post-migration monitoring

SQL active requests:

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
```

Blocking:

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

Query Store:

```sql
SELECT TOP (20)
    qt.query_sql_text,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_logical_io_reads,
    rs.count_executions
FROM sys.query_store_runtime_stats AS rs
JOIN sys.query_store_plan AS p ON rs.plan_id = p.plan_id
JOIN sys.query_store_query AS q ON p.query_id = q.query_id
JOIN sys.query_store_query_text AS qt ON q.query_text_id = qt.query_text_id
ORDER BY rs.avg_duration DESC;
```

App metrics:

| Metric | Pass |
|---|---|
| API p95/p99 | Không tăng vượt ngưỡng demo đã thống nhất. |
| Error rate | Không tăng bất thường. |
| Hikari pending | 0 hoặc không kéo dài. |
| Hikari timeout | 0. |

Azure metrics:

| Metric | Pass |
|---|---|
| CPU | Không sustained > ngưỡng demo. |
| Data IO | Không sustained > ngưỡng demo. |
| Log IO | Không sustained > ngưỡng demo. |
| Sessions/workers | Không gần giới hạn. |

## Phần 7 - RCA nếu demo lỗi

```markdown
# Incident RCA - Migration Demo

## Summary
Sau cutover demo, SalesApp login vào target thất bại do database user chưa map đúng với app login.

## Timeline
| Time | Event |
|---|---|
| 09:00 | Cutover demo started |
| 09:15 | Database online on target |
| 09:20 | App smoke test failed |
| 09:22 | Error log captured, secret removed |
| 09:30 | User mapping fixed in lab |
| 09:35 | App smoke test passed |

## Impact
- Demo read/write API không chạy trong 15 phút.
- Không mất dữ liệu.

## Root cause
Migration rehearsal chưa validate đầy đủ login/user mapping cho app principal.

## Evidence
- JDBC login error with secret removed.
- Target database principal check.
- Smoke test failed then passed after fix.

## Corrective action
- Add user mapping validation to pre-cutover checklist.

## Preventive action
- Add security validation gate to run sheet.
- Require app owner smoke test before go decision.
```

RCA không đạt nếu chỉ mô tả lỗi mà không có preventive action.

## Dossier cuối ngày 7

```markdown
# Final Demo Report - Junior Azure SQL DBA

## Scope
- App:
- Source:
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Target:
- Database:
- Migration mode:

## Readiness
| Area | Finding | Status |
|---|---|---|
| Target choice | | |
| Compatibility | | |
| Network | | |
| Security | | |
| Capacity | | |

## Baseline
| Area | Evidence | Finding |
|---|---|---|
| SQL | | |
| Query Store | | |
| App | | |

## Execution
| Step | Status | Evidence |
|---|---|---|
| Backup/restore point | | |
| Migration | | |
| Validation | | |
| Monitoring | | |

## Validation
| Check | Source | Target | Status |
|---|---|---|---|
| Row count | | | |
| Object count | | | |
| Security/app login | | | |
| Read API | | | |
| Write API | | | |
| Performance | | | |

## Decision
- Go/No-Go:
- Rollback trigger:
- Known issues:
- Production readiness:
- Next actions:
```

## Chấm điểm gợi ý

| Hạng mục | Điểm tối đa |
|---|---:|
| Target readiness: compatibility/network/security/capacity | 20 |
| Migration run sheet và execution evidence | 20 |
| Validation matrix đầy đủ | 25 |
| Rollback/go-no-go | 15 |
| Monitoring sau cutover | 10 |
| RCA/communication/vấn đáp | 10 |

## Câu hỏi vấn đáp

Đạt nếu người học trả lời rõ:

- Evidence nào chứng minh migration pass?
- Evidence nào chưa đủ để nói production-ready?
- Khi nào rollback?
- Ai approve go/no-go?
- Nếu app timeout sau cutover, kiểm tra app pool, SQL waits, blocking, network theo thứ tự nào?
- Nếu row count match nhưng API sai dữ liệu, kiểm tra gì tiếp?
- Nếu target chậm hơn source, phân biệt cold cache, tier thiếu tài nguyên, statistics và plan regression thế nào?

## Lỗi thường gặp

- Gọi target chung chung là Azure SQL, không phân biệt Database/Managed Instance.
- Demo chỉ chạy tool migration, không nói baseline.
- Chỉ validate row count, bỏ qua security/app/performance.
- Không có rollback trigger.
- Không test write path.
- Không có monitoring window.
- RCA chỉ mô tả lỗi, không có preventive action.
