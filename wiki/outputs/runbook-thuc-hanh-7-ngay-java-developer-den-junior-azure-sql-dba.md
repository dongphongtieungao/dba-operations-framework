---
id: runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
title: "Runbook thực hành 7 ngày: Java developer đến Junior Azure SQL DBA"
type: output
created: 2026-05-15
updated: 2026-05-18
covers:
  - sources/master-dba-runbook-backend-java-azure-sql
  - sources/dba-operations-framework
  - sources/sql-server-2022-administration-inside-out
  - sources/azure-sql-documentation
  - sources/azure-database-migration-guides
  - sources/azure-database-migration-service-documentation
  - sources/runbook-database-health-check
  - sources/runbook-backup-verification
  - sources/runbook-restore-validation
  - sources/runbook-capacity-report
  - sources/runbook-user-provisioning
  - sources/runbook-failover-execution
  - concepts/dba-production-mindset
  - concepts/dba-operations-framework
  - concepts/dba-service-catalog
  - concepts/dba-operating-model
  - concepts/dba-raci-matrix
  - concepts/dba-evidence-driven-operations
  - concepts/dba-policy-sop-runbook-layering
  - concepts/sql-server-execution-plan-analysis
  - concepts/sql-server-blocking-and-deadlock-investigation
  - concepts/sql-server-query-store-analysis
  - concepts/azure-sql-monitoring-and-observability
  - concepts/azure-sql-managed-instance
  - concepts/database-migration
  - concepts/migration-assessment
---

# Runbook thực hành 7 ngày: Java developer đến Junior Azure SQL DBA

## 1. Mục tiêu và cách dùng

Runbook này dành cho Java backend developer đã biết SQL Server cơ bản và muốn bước vào vai trò Junior Azure SQL DBA. Sau 7 ngày, người học phải chứng minh được ba năng lực:

1. Điều tra database bằng bằng chứng, không đoán.
2. Thực hiện thao tác DBA theo ticket, runbook, checklist, evidence và tiêu chí nghiệm thu.
3. Demo migration từ SQL Server 2019 lên một trong hai target được chọn rõ ràng: **Azure SQL Database** hoặc **Azure SQL Managed Instance**, bằng baseline, assessment, rehearsal, validation, monitoring và rollback plan.

Đây không phải giáo trình học SQL từ đầu. Người học đã cần biết table, index, stored procedure, transaction, connection string và cách đọc log ứng dụng Java.

Junior trong tài liệu này không có nghĩa là "biết vài câu lệnh cơ bản". Junior Azure SQL DBA là người có thể xử lý việc vận hành cấp 1/cấp 2 dưới sự giám sát: nhận ticket đúng, thu evidence đúng, phân biệt rủi ro, chạy runbook đã duyệt, biết khi nào phải escalation và không làm mất dữ liệu trong lúc xử lý sự cố.

## 1.1. Chuẩn năng lực Junior Azure SQL DBA

| Vùng năng lực | Junior phải làm được | Chưa yêu cầu ở mức junior |
|---|---|---|
| Azure SQL platform | Phân biệt Azure SQL Database, Azure SQL Managed Instance và SQL Server on Azure VM ở mức chọn target vận hành. | Thiết kế kiến trúc enterprise đa vùng phức tạp từ đầu. |
| Connectivity | Kiểm tra firewall, private endpoint, DNS, TLS, JDBC connection string và lỗi login phổ biến. | Thiết kế toàn bộ hub-spoke network hoặc landing zone. |
| Security | Tạo user/role theo least privilege, hiểu SQL auth, Microsoft Entra ID, auditing, TDE, Defender, data masking ở mức vận hành. | Thiết kế mô hình Zero Trust đầy đủ hoặc Always Encrypted nâng cao. |
| Backup/restore | Kiểm tra backup status, PITR/LTR, restore test, RPO/RTO và restore validation. | Thiết kế chiến lược DR toàn doanh nghiệp nhiều region. |
| Monitoring | Đọc Azure metrics, Query Store, wait, blocking, deadlock, failed login, CPU/IO/log IO/session saturation. | Xây observability platform hoàn chỉnh. |
| Performance | Đọc actual plan, logical reads, key lookup, sort/hash spill, implicit conversion, parameter sniffing cơ bản. | Tối ưu workload phức tạp nhiều subsystem hoặc engine internals chuyên sâu. |
| Change | Review script, rollback, maintenance window, post-change monitoring và evidence. | Chủ trì CAB cho thay đổi rủi ro cao. |
| Migration | Chạy assessment, rehearsal, validation, app smoke test và rollback plan theo run sheet. | Dẫn chương trình migration quy mô lớn nhiều ứng dụng. |
| Incident | Triage P1/P2, mitigate theo runbook, ghi timeline/RCA sơ bộ và escalation đúng người. | Là incident commander chính cho sự cố lớn. |
| Automation | Chạy automation/runbook đã duyệt, đọc output, phát hiện lỗi và ghi evidence. | Thiết kế automation framework/phê duyệt pipeline chuẩn tổ chức. |

## 1.1.1. Quy ước tránh nhập nhằng Azure SQL

Trong toàn bộ chương trình:

| Cách gọi | Nghĩa chính xác |
|---|---|
| Azure SQL Database | Dịch vụ PaaS ở mức database, chạy trên logical server. |
| Azure SQL Managed Instance hoặc MI | Dịch vụ PaaS ở mức instance, gần SQL Server hơn về bề mặt vận hành. |
| Azure SQL | Chỉ dùng như tên họ sản phẩm chung; khi làm lab phải ghi rõ là Database hay Managed Instance. |
| Track A | Bài thực hành theo Azure SQL Database. |
| Track B | Bài thực hành theo Azure SQL Managed Instance. |

Nếu một bài nói "target" hoặc "Azure SQL target", người học phải điền rõ:

```text
Target type: Azure SQL Database / Azure SQL Managed Instance
Target name:
Endpoint/FQDN:
Network path:
Auth model:
```

Không đạt nếu chỉ ghi "Azure SQL" mà không phân biệt hai loại.

## 1.2. Tiêu chuẩn "không hời hợt"

Một bài lab chỉ đạt khi có đủ bốn lớp:

1. **Kỹ thuật:** query/script/portal action chạy được.
2. **Bằng chứng:** có output trước và sau, không chỉ mô tả miệng.
3. **Quyết định:** giải thích được pass/fail, go/no-go, apply/defer/rollback.
4. **Vận hành:** có ticket, owner, rủi ro, tiêu chí nghiệm thu và action tiếp theo.

Nếu thiếu một trong bốn lớp trên, bài lab chỉ là bài tập kỹ thuật, chưa đạt chuẩn Junior Azure SQL DBA.

## 1.3. Tiêu chuẩn "kỹ lưỡng"

Kỹ lưỡng nghĩa là người học không chỉ biết làm từng bước, mà còn biết kiểm soát sai lệch, giới hạn quyền hạn và chất lượng bằng chứng. Mỗi chủ đề phải đi qua sáu tầng:

| Tầng | Câu hỏi bắt buộc |
|---|---|
| Context | Hệ thống nào, môi trường nào, app nào, owner nào, severity nào? |
| Baseline | Trạng thái bình thường là gì, số liệu trước thay đổi là gì? |
| Diagnosis | Evidence nào chứng minh giả thuyết, evidence nào phản bác giả thuyết? |
| Action | Hành động được phép là gì, ai approve, rollback thế nào? |
| Validation | Sau thao tác, kiểm tra bằng metric/query/smoke test nào? |
| Learning | Điều gì phải thêm vào SOP, alert, dashboard, runbook hoặc backlog? |

Một câu trả lời kỹ lưỡng phải nêu được cả **điều biết chắc**, **điều chưa biết**, **rủi ro còn lại** và **bước kiểm chứng tiếp theo**.

## 1.4. Điều kiện đầu vào của người học

Trước khi bắt đầu, người học phải tự kiểm tra:

| Nhóm | Yêu cầu tối thiểu |
|---|---|
| SQL/T-SQL | Biết `JOIN`, `GROUP BY`, index cơ bản, stored procedure, transaction, isolation level cơ bản. |
| Java backend | Hiểu JDBC, connection string, exception log, HikariCP, timeout, API latency. |
| SQL Server | Biết database, login/user, backup/restore cơ bản, SQL Agent ở mức nhận biết. |
| Azure | Biết resource group, region, VNet/subnet ở mức đọc hiểu, Azure Portal cơ bản. |
| Vận hành | Biết ticket, incident, change, severity, rollback, owner, evidence. |

Nếu thiếu một nhóm, vẫn có thể học nhưng phải ghi rõ là **learning gap** trong báo cáo ngày 1.

## 1.5. Chuẩn evidence

Evidence đạt chuẩn phải đủ để một DBA khác đọc lại và tái hiện được quyết định.

| Loại evidence | Đạt | Không đạt |
|---|---|---|
| Query output | Có thời gian chạy, database, môi trường, câu query và kết quả chính. | Chỉ ghi "đã chạy query". |
| Execution plan | Có actual plan hoặc plan_id/hash, operator chính, estimated vs actual rows, warning. | Chỉ ghi "plan xấu". |
| Metric | Có khoảng thời gian, timezone, metric name, giá trị trước/sau. | Screenshot không có thời gian hoặc ngữ cảnh. |
| App log | Có endpoint, correlation/timestamp, exception đã che secret. | Paste log lộ password/token. |
| Change evidence | Có script version, reviewer, start/end time, output, validation, rollback status. | Chỉ ghi "deploy thành công". |
| Migration evidence | Có source/target, row count, object count, app smoke test, security check, performance check. | Chỉ ghi "restore xong". |

Quy tắc che dữ liệu:

- Không ghi password, token, connection string đầy đủ có secret.
- Không xuất dữ liệu khách hàng thật nếu không cần.
- Với email, phone, account number, chỉ dùng sample đã mask.
- Evidence production phải lưu ở nơi có kiểm soát truy cập.

## 1.6. Ranh giới quyền hạn của Junior

Junior Azure SQL DBA được kỳ vọng **đề xuất và thực hiện thao tác đã duyệt**, không tự quyết định các thay đổi rủi ro cao.

| Tình huống | Junior được làm | Phải escalation |
|---|---|---|
| Query chậm P3/P4 | Thu evidence, phân tích plan, đề xuất index/rewrite. | Tự tạo index production trên bảng lớn. |
| Blocking | Xác định blocker, impact, transaction, đề xuất mitigation. | Kill session production nếu chưa được approve. |
| Backup failed | Kiểm tra job/log, chạy backup bù nếu SOP cho phép. | Backup failure vượt RPO hoặc lỗi lặp lại. |
| Restore | Chạy restore lab/non-prod theo runbook. | Restore production ghi đè dữ liệu. |
| Access request | Kiểm tra ticket, approval, cấp quyền theo mẫu. | Cấp quyền privileged/break glass. |
| Migration | Chạy rehearsal, validation, report. | Quyết định cutover production. |
| Incident P1/P2 | Triage, thu evidence, thông báo/escalation. | Là incident commander nếu chưa được chỉ định. |

## 1.7. Cách chấm

Mỗi ngày chấm theo thang 100 điểm:

| Hạng mục | Điểm |
|---|---:|
| Kỹ thuật chạy đúng | 25 |
| Evidence đầy đủ và sạch secret | 20 |
| Phân tích nguyên nhân hoặc rủi ro | 20 |
| Quyết định vận hành: go/no-go, rollback, escalation | 20 |
| Giao tiếp: report rõ, timeline rõ, owner rõ | 15 |

Điều kiện qua ngày:

- Tối thiểu 75/100.
- Không có lỗi nghiêm trọng về secret trong evidence.
- Không bỏ qua rollback/validation ở bài có thay đổi.
- Không kết luận root cause khi evidence chưa đủ.

## 2. Nguyên tắc vận hành bắt buộc

| Nguyên tắc | Nghĩa trong thực hành |
|---|---|
| Evidence-first | Mọi kết luận phải có metric, query output, execution plan, log, wait, lock hoặc ticket. |
| Ticket-first | Thao tác DBA production cần ticket hoặc emergency approval. Lab vẫn tập ghi ticket giả lập. |
| Least privilege | Không cấp quyền rộng hơn nhu cầu, không dùng tài khoản cá nhân cho service account. |
| Change-safe | Có kế hoạch, rollback, validation và post-change monitoring. |
| Restore-proven | Backup chỉ được coi là đáng tin khi restore test hoặc validation đạt. |
| App-aware | DBA phải hiểu JDBC, HikariCP, timeout, connection pool và smoke test app. |
| Communication | Có timeline, owner, decision, risk và next action rõ ràng. |

Những việc không được làm trong khóa học:

1. Không kết luận "thiếu index" khi chưa xem IO và execution plan.
2. Không kill session khi chưa biết blocker, transaction và rủi ro rollback.
3. Không restart SQL Server hoặc Managed Instance để xóa triệu chứng.
4. Không đổi schema/index production nếu không có rollback và post-check.
5. Không nói migration thành công nếu chưa validate data, schema, security, app và performance.
6. Không ghi password, token hoặc dữ liệu nhạy cảm vào evidence.

## 3. Khung vận hành dùng xuyên suốt

Mỗi ngày học đều phải tạo một gói evidence theo mẫu tối thiểu:

| Trường | Nội dung |
|---|---|
| Ticket ID | Ví dụ `LAB-DBA-D1` |
| Operation type | Baseline, tuning, blocking, monitoring, migration, restore, access |
| Người thực hiện | Tên người học |
| Environment | Lab, Dev, UAT hoặc Production simulation |
| Application/service | Ví dụ `SalesApp` |
| Instance/database | SQL Server source, target type rõ ràng: Azure SQL Database hoặc Azure SQL Managed Instance, `SalesAppDB` |
| Risk level | Low, Medium, High |
| Input | Query, script, request, assessment hoặc alert |
| Output | Kết quả thực tế, file/report, ảnh dashboard nếu có |
| Validation | Điều kiện pass/fail |
| Next action | Việc còn lại và owner |

Mapping với DBA Operations Framework:

| Hoạt động trong khóa học | Service/SOP/Runbook tương ứng |
|---|---|
| Baseline và health check | Daily Database Health Check, Runbook Database Health Check |
| Query chậm | SOP Performance Troubleshooting, SOP Query Tuning Review |
| Blocking/deadlock | SOP Blocking and Deadlock Handling |
| Cấp quyền app | SOP User and Permission Management, Runbook User Provisioning |
| Backup/restore validation | Backup and Restore Policy, Runbook Backup Verification, Runbook Restore Validation |
| Migration/cutover | Change Management Policy, Request for Change, Migration run sheet |
| Monitoring sau thay đổi | Database Monitoring Standard, Database Alerting Standard |
| Báo cáo cuối khóa | Template Operation Evidence, Template Monthly DBA Report, PIR/RCA template |

## 4. Môi trường lab chuẩn

| Thành phần | Yêu cầu |
|---|---|
| Source | SQL Server 2019 Developer/Standard/Enterprise lab |
| Track A target | Azure SQL Database `SalesAppDB` trên logical server |
| Track B target | Azure SQL Managed Instance có database `SalesAppDB_MI` |
| Database | `SalesAppDB` |
| App | Java Spring Boot hoặc app JDBC nhỏ dùng HikariCP |
| Tool | SSMS hoặc Azure Data Studio, Azure Portal, Azure CLI/PowerShell nếu có |
| Migration tool | DMS, native backup/restore vào MI, BACPAC/DACPAC hoặc tool được phép; phải ghi tool phù hợp với track |
| Evidence store | Folder/wiki/ticket giả lập, không chứa secret |

## 4.1. Azure Portal map cho người học junior

Azure Portal không thay thế SQL evidence, nhưng là nơi junior DBA phải biết dùng để kiểm tra tài nguyên cloud, network, restore, metric và thay đổi vận hành. Mỗi lần dùng Portal phải ghi **resource name, time window, blade đã mở, kết luận và action**, không chỉ chụp màn hình.

| Ngày | Portal blade cần biết | Dùng để làm gì | Evidence cần ghi |
|---|---|---|---|
| 0 | Resource groups, SQL databases, SQL servers, Managed instances | Tạo hoặc kiểm tra target lab. | Subscription, resource group, region, target type, SKU/tier, owner, cleanup date. |
| 0 | Networking, Firewalls and virtual networks, Private endpoint connections | Kiểm tra public firewall/private endpoint/VNet path. | FQDN, firewall rule/private endpoint, DNS expectation, app test host. |
| 0 | Microsoft Entra admin, SQL authentication setting nếu có | Xác nhận auth model cho lab. | Admin/bootstrap principal, app principal plan, secret handling note. |
| 5 | Backups, Restore, Point-in-time restore | Chọn restore point và restore sang database mới. | Retention, earliest restore point, requested restore point, restore target name. |
| 5 | Activity log, Deployment details | Theo dõi restore/provision operation. | Start/end time, status, failure message nếu có. |
| 6 | Metrics | Đọc CPU, data IO, log IO, sessions, workers, storage. | Time range, aggregation, max/avg, finding. |
| 6 | Alerts, Diagnostic settings, Logs nếu tổ chức có bật | Kiểm tra cảnh báo và log vận hành. | Alert name, fired time, severity, action group/log destination. |
| 7 | Overview, Networking, Connection strings, Metrics, Activity log | Readiness và monitoring sau cutover. | Go/no-go evidence, rollback trigger, post-cutover metric window. |

Quy tắc dùng Portal trong khóa học:

- Không đổi setting production hoặc lab chung nếu chưa có ticket/approval.
- Không mở firewall rộng để "test cho nhanh" nếu không ghi risk và cleanup.
- Không lưu secret từ Portal vào evidence.
- Screenshot chỉ là phụ; phải có nhận xét và quyết định đi kèm.
- Nếu Portal khác tên blade do giao diện Azure thay đổi, ghi tên resource và ý nghĩa thao tác thay vì phụ thuộc vào nhãn màn hình.

Track tối thiểu:

| Track | Mục tiêu |
|---|---|
| Track A - Azure SQL Database | Hiểu database-level PaaS, firewall/private endpoint, contained user, app connectivity và validation ở mức database. |
| Track B - Azure SQL Managed Instance | Hiểu instance-level PaaS, VNet/subnet, login-user mapping, SQL Agent/compatibility và migration gần SQL Server hơn. |

Người học nên làm cả hai track nếu có quyền và ngân sách lab. Nếu chỉ làm một track, dossier phải ghi rõ track còn lại là learning gap hoặc design-only.

HikariCP cấu hình mẫu cho lab:

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

## 5. Database lab tối thiểu

```sql
CREATE DATABASE SalesAppDB;
GO

ALTER DATABASE SalesAppDB SET RECOVERY FULL;
GO

USE SalesAppDB;
GO

CREATE TABLE dbo.Customers
(
    CustomerId INT IDENTITY(1,1) NOT NULL,
    Email NVARCHAR(320) NOT NULL,
    FullName NVARCHAR(200) NOT NULL,
    City NVARCHAR(100) NULL,
    CreatedAt DATETIME2(3) NOT NULL DEFAULT SYSUTCDATETIME(),
    Status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    CONSTRAINT PK_Customers PRIMARY KEY CLUSTERED (CustomerId),
    CONSTRAINT UQ_Customers_Email UNIQUE (Email)
);

CREATE TABLE dbo.Orders
(
    OrderId BIGINT IDENTITY(1,1) NOT NULL,
    CustomerId INT NOT NULL,
    OrderStatus VARCHAR(20) NOT NULL,
    TotalAmount DECIMAL(18,2) NOT NULL,
    CreatedAt DATETIME2(3) NOT NULL,
    UpdatedAt DATETIME2(3) NULL,
    Notes NVARCHAR(1000) NULL,
    CONSTRAINT PK_Orders PRIMARY KEY CLUSTERED (OrderId),
    CONSTRAINT FK_Orders_Customers FOREIGN KEY (CustomerId)
        REFERENCES dbo.Customers(CustomerId)
);

CREATE TABLE dbo.PaymentEvents
(
    PaymentEventId BIGINT IDENTITY(1,1) NOT NULL,
    OrderId BIGINT NOT NULL,
    EventType VARCHAR(40) NOT NULL,
    ProviderRef VARCHAR(100) NULL,
    Payload NVARCHAR(MAX) NULL,
    CreatedAt DATETIME2(3) NOT NULL DEFAULT SYSUTCDATETIME(),
    CONSTRAINT PK_PaymentEvents PRIMARY KEY CLUSTERED (PaymentEventId)
);
GO
```

Stored procedure mẫu:

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

    SELECT o.OrderId, o.CustomerId, o.TotalAmount, o.CreatedAt
    FROM dbo.Orders AS o
    WHERE o.OrderStatus = @OrderStatus
      AND o.CreatedAt >= @FromDate
      AND o.CreatedAt < @ToDate
    ORDER BY o.CreatedAt DESC;
END;
GO
```

Seed data có thể dùng script nội bộ của team. Nếu tự tạo dữ liệu, cần đủ lớn để query scan, lookup, sort, blocking và migration validation có ý nghĩa.

## 5.1. Bộ workload bắt buộc cho lab

Để bài học đủ sâu, lab phải có ít nhất bốn loại workload:

| Workload | Mục đích | Dấu hiệu cần tạo được |
|---|---|---|
| Read by customer | Tuning index và đọc execution plan. | Scan hoặc key lookup trước khi tối ưu. |
| Search by status/date | Kiểm tra composite index, sort, parameter sensitivity. | Reads cao hoặc sort lớn. |
| Payment update | Transaction, blocking và rollback smoke test. | Lock trên `Orders` hoặc `PaymentEvents`. |
| Report query | Capacity/performance baseline. | CPU/IO rõ, có Query Store history. |

Mỗi workload cần có:

- Stored procedure hoặc query cố định.
- Tần suất chạy giả lập.
- Expected latency.
- Evidence trước và sau khi tuning/migration.

## 5.2. Bộ lỗi bắt buộc phải mô phỏng

| Lỗi | Người học phải chứng minh |
|---|---|
| Missing/non-covering index | Logical reads giảm sau khi index phù hợp. |
| Blocking do transaction mở | Xác định head blocker và impact với app. |
| Deadlock hoặc deadlock-like scenario | Biết đọc victim, object và transaction order ở mức cơ bản. |
| Login/access failure | Phân biệt login fail, user mapping fail, permission denied. |
| Connection timeout | Phân biệt network/auth/pool/session saturation. |
| Migration validation mismatch | Biết no-go khi row count/object/security/app check chưa đạt. |

## 6. Lộ trình 8 file thành một thể thống nhất

Runbook chính là xương sống của chương trình. Tám file lời giải ngày 0-7 là phần hướng dẫn chi tiết để mentor hoặc người học đối chiếu khi làm bài. Không học rời từng file; mỗi ngày phải tạo evidence dùng lại cho ngày sau.

| File | Vai trò trong chương trình | Sản phẩm chuyển tiếp |
|---|---|---|
| [[outputs/loi-giai-ngay-0-tao-azure-sql-database-moi-tren-cloud]] | Tạo môi trường cloud/lab đúng cách. | Azure SQL Database hoặc Managed Instance lab, network/auth/cleanup note. |
| [[outputs/loi-giai-ngay-1-baseline-va-operating-model]] | Đặt nền vận hành và baseline. | Baseline dossier để so sánh tuning, monitoring, migration. |
| [[outputs/loi-giai-ngay-2-io-execution-plan-index-review]] | Điều tra query chậm bằng IO/plan/index. | Query tuning dossier và change record. |
| [[outputs/loi-giai-ngay-3-blocking-deadlock-transaction]] | Xử lý lock, transaction và timeout. | Locking incident dossier và RCA mini. |
| [[outputs/loi-giai-ngay-4-query-store-regression-release-safety]] | Dùng Query Store cho regression/release safety. | Regression dossier và mitigation decision. |
| [[outputs/loi-giai-ngay-5-backup-restore-azure-sql]] | Chứng minh backup có thể restore. | Restore evidence, RPO/RTO, cleanup. |
| [[outputs/loi-giai-ngay-6-doc-metric-va-theo-doi-azure-sql]] | Đọc metric và giám sát hiện trạng. | Health report, app correlation, next action. |
| [[outputs/loi-giai-ngay-7-readiness-migration-demo-cutover]] | Tổng hợp readiness, migration, demo, monitoring, RCA. | Final demo report và go/no-go. |

Chuỗi phụ thuộc:

```text
Ngày 0 tạo target/lab
  -> Ngày 1 thu baseline
  -> Ngày 2 hiểu query và index
  -> Ngày 3 hiểu transaction/lock
  -> Ngày 4 kiểm soát regression
  -> Ngày 5 chứng minh restore
  -> Ngày 6 đọc monitoring
  -> Ngày 7 demo migration có go/no-go, rollback và RCA
```

| Ngày | Trọng tâm | Sản phẩm bắt buộc | Dùng lại ở ngày sau |
|---|---|---|---|
| 0 | Provision Azure SQL Database/Managed Instance lab | Target dossier, connection test, cleanup plan | Ngày 1, 5, 6, 7 |
| 1 | Mindset, service catalog, baseline | Baseline report + evidence pack | Ngày 2, 4, 6, 7 |
| 2 | IO, execution plan, index review | Query tuning ticket + before/after evidence | Ngày 4, 6, 7 |
| 3 | Transaction, blocking, deadlock | Blocking/deadlock RCA mini report | Ngày 6, 7 |
| 4 | Query Store, regression, release safety | Plan regression report + mitigation decision | Ngày 6, 7 |
| 5 | Backup/restore Azure SQL | PITR, restore validation, RPO/RTO, cleanup | Ngày 7 |
| 6 | Đọc metric và monitoring Azure SQL | Azure metrics, Query Store, DMV, app correlation | Ngày 7 |
| 7 | Readiness, migration rehearsal và demo cutover | Target design, run sheet, validation, rollback, RCA | Final demo |

Độ sâu tối thiểu theo từng ngày:

| Ngày | Câu hỏi phải trả lời được |
|---|---|
| 0 | Target lab có được tạo an toàn, kết nối được, có network/auth rõ và cleanup plan không? |
| 1 | Nếu API chậm, evidence nào chứng minh database là hoặc không phải bottleneck? |
| 2 | Vì sao index đề xuất tốt cho read nhưng có thể làm write chậm hơn? |
| 3 | Khi nào được kill blocker, ai quyết định và rủi ro rollback là gì? |
| 4 | Nếu Query Store có hai plan, chọn mitigation nào và vì sao? |
| 5 | Restore xong nhưng app lỗi login hoặc row count lệch thì validation đang thiếu gì? |
| 6 | Metric nào chứng minh vấn đề là CPU, Data IO, Log IO, session/worker, blocking hay app pool? |
| 7 | App Java không kết nối được target thì kiểm tra theo thứ tự nào, readiness có đủ để migration/cutover không, và metric nào kích hoạt rollback? |

# Ngày 0 - Tạo Azure SQL target lab trên cloud

## Mục tiêu

Người học tạo được môi trường lab tối thiểu trên Azure để các ngày sau có target thật để kết nối, restore, monitoring và demo migration. Ngày 0 không chấm theo số lượng tài nguyên tạo được, mà chấm theo việc người học ghi rõ target type, network path, auth model, quyền truy cập, chi phí và cleanup.

Lời giải chi tiết: [[outputs/loi-giai-ngay-0-tao-azure-sql-database-moi-tren-cloud]]

## Lab 0.1 - Chọn target lab

```markdown
# LAB-D0 - Azure SQL Target Provisioning

## Target choice
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Region:
- Resource group:
- Naming convention:
- Reason:
- Cost guardrail:

## Access
- Admin/operator:
- App principal:
- Auth model: SQL auth / Microsoft Entra / mixed for lab
- Secret storage note:

## Network
- Public firewall / private endpoint / MI VNet:
- App test host:
- DNS expectation:

## Cleanup
- Owner:
- Cleanup date:
- Resources to remove:
```

## Lab 0.2 - Setup tool client để vận hành Azure SQL

Người học phải chuẩn bị ít nhất một tool SQL client và một đường vận hành qua Azure Portal. Không đạt nếu chỉ tạo database xong nhưng không kết nối/kiểm tra được từ máy vận hành.

| Tool | Dùng để làm gì | Bắt buộc/khuyến nghị |
|---|---|---|
| Azure Portal | Tạo resource, kiểm tra overview, networking, connection strings, metrics, activity log, restore. | Bắt buộc |
| SSMS | Kết nối, chạy query, xem object, backup/restore MI hoặc tác vụ quen SQL Server. | Bắt buộc nếu dùng Windows |
| Azure Data Studio | Kết nối đa nền tảng, notebook/query, phù hợp lab nhẹ. | Bắt buộc nếu không dùng SSMS |
| sqlcmd | Smoke test kết nối nhanh, dùng trong script/checklist. | Khuyến nghị |
| Azure CLI hoặc PowerShell | Kiểm tra resource, firewall, automation nhẹ. | Khuyến nghị |
| Java app/HikariCP test | Kiểm tra đường kết nối thật của app. | Bắt buộc nếu có app lab |

Client setup checklist:

```markdown
# LAB-D0-CLIENT - DBA Client Setup

## Workstation
- OS:
- Network location: home/VPN/app subnet/bastion:
- Public IP if using firewall lab:

## Installed tools
| Tool | Version | Purpose | Status |
|---|---|---|---|
| Azure Portal access | N/A | Resource operations | |
| SSMS | | SQL query/admin | |
| Azure Data Studio | | SQL query/notebook | |
| sqlcmd | | Smoke test | |
| Azure CLI/PowerShell | | Resource check | |

## Connection profiles
| Target | Client | Auth model | Result |
|---|---|---|---|
| Azure SQL Database | SSMS/ADS/sqlcmd | SQL auth/Entra | |
| Managed Instance | SSMS/ADS/sqlcmd | SQL auth/Entra | |

## Evidence
- Screenshot is optional:
- Query output is required:
- Secret stored outside evidence:
```

Smoke test bằng SQL client:

```sql
SELECT
    @@SERVERNAME AS server_name,
    DB_NAME() AS database_name,
    SUSER_SNAME() AS login_name,
    USER_NAME() AS database_user,
    SYSUTCDATETIME() AS checked_at_utc;
```

Nếu dùng `sqlcmd`, mẫu lệnh:

```powershell
sqlcmd -S "<server-or-mi-fqdn>" -d "SalesAppDB" -U "<user>" -P "<password>" -N -Q "SELECT @@SERVERNAME, DB_NAME(), SUSER_SNAME(), SYSUTCDATETIME();"
```

Không ghi password thật vào dossier.

## Lab 0.3 - Provision và connection test

Track A - Azure SQL Database:

- Tạo logical server và database lab.
- Cấu hình firewall hoặc private endpoint theo mục tiêu lab.
- Tạo database user/app principal tối thiểu.
- Test kết nối bằng SSMS/Azure Data Studio/app host.

Track B - Azure SQL Managed Instance:

- Tạo hoặc dùng managed instance lab đã có.
- Kiểm tra VNet/subnet/DNS/NSG route từ app test host.
- Tạo login/user hoặc Entra principal theo mô hình lab.
- Test kết nối tới MI FQDN.

## Lab 0.4 - Azure Portal evidence bắt buộc

Người học mở Azure Portal và ghi lại:

```markdown
# Azure Portal Evidence - Day 0

## Resource identity
- Subscription:
- Resource group:
- Target type:
- Target name:
- Region:
- SKU/tier:
- Tags:

## Portal checks
| Portal area | What checked | Finding | Action |
|---|---|---|---|
| Overview | Resource status/FQDN | | |
| Networking | Firewall/private endpoint/VNet | | |
| Connection strings | JDBC endpoint pattern | | |
| Microsoft Entra/admin/auth | Auth model | | |
| Metrics | Basic CPU/storage visible | | |
| Activity log | Provisioning operation status | | |

## Decision
- Ready for day 1 baseline: yes/no
- Blocker:
- Cleanup owner/date:
```

## Lab 0.5 - Vận hành Azure DB bằng Azure Portal

Người học thực hiện một vòng đọc/kiểm tra vận hành trên Portal. Không cần thay đổi cấu hình nếu không có quyền; nếu chỉ đọc, vẫn phải ghi kết luận.

| Tác vụ Portal | Blade/khu vực | Người học phải làm được |
|---|---|---|
| Xác định database/server/MI | Overview | Đọc status, FQDN, region, SKU/tier. |
| Lấy mẫu connection string | Connection strings | Lấy endpoint pattern, xóa secret trước khi lưu. |
| Kiểm tra network access | Networking / Firewalls / Private endpoint connections | Nói được app đi public firewall hay private endpoint/VNet. |
| Chạy query nhanh nếu được phép | Query editor hoặc dùng SSMS/ADS thay thế | Chạy smoke query và lưu output. |
| Xem metric cơ bản | Metrics | Đọc CPU/storage/session trong 30-60 phút gần nhất. |
| Xem thay đổi gần đây | Activity log | Xem provisioning/network/security operation gần nhất. |
| Kiểm tra restore entry point | Backups/Restore | Biết restore/PITR bắt đầu từ đâu, không bấm restore production nếu chưa có approval. |

Mẫu kết luận:

```markdown
## Portal operation drill
- Resource status:
- Connection endpoint verified:
- Network path understood:
- Smoke query result:
- Metrics finding:
- Recent Activity log finding:
- Restore entry point identified:
- Risk/action:
```

Validation tối thiểu:

```sql
SELECT
    @@SERVERNAME AS server_name,
    DB_NAME() AS database_name,
    SUSER_SNAME() AS login_name,
    USER_NAME() AS database_user,
    SYSUTCDATETIME() AS validated_at_utc;
```

## Checklist cuối ngày 0

- [ ] Target type ghi rõ, không dùng chữ "Azure SQL" chung chung.
- [ ] Có ít nhất một SQL client kết nối thành công: SSMS, Azure Data Studio hoặc sqlcmd.
- [ ] Biết dùng Azure Portal để kiểm tra Overview, Networking, Connection strings, Metrics và Activity log.
- [ ] Kết nối thành công từ máy/operator hoặc app test host.
- [ ] Auth model và app principal có ghi chú.
- [ ] Network path có evidence.
- [ ] Có cost guardrail và cleanup plan.
- [ ] Không lưu secret trong evidence.

## Deliverable ngày 0 - Target provisioning dossier

```markdown
# Target Provisioning Dossier

## Scope
- Target type:
- Target name/FQDN:
- Region:
- Resource group:
- Owner:

## Connectivity
| Check | Result | Evidence |
|---|---|---|
| DNS/FQDN | | |
| Firewall/private endpoint/VNet | | |
| SQL connection | | |
| App test host path | | |

## Security
| Principal | Purpose | Permission scope |
|---|---|---|

## Cost and cleanup
- SKU/tier:
- Expected cost risk:
- Cleanup owner:
- Cleanup date:
```

# Ngày 1 - DBA mindset, service catalog và baseline

## Mục tiêu

Người học hiểu Junior Azure SQL DBA làm việc theo service request, evidence và operating model. Kết thúc ngày 1 phải có baseline đủ để so sánh trước/sau migration.

Lời giải chi tiết: [[outputs/loi-giai-ngay-1-baseline-va-operating-model]]

## Lab 1.1 - Tạo ticket giả lập

```markdown
# LAB-DBA-D1 - Baseline SalesAppDB before migration

## Request
Thu baseline cho SalesAppDB trước khi migration lên Azure SQL Database hoặc Azure SQL Managed Instance.

## Input
- App: SalesApp
- Source: SQL Server 2019
- Database: SalesAppDB
- Target type: Azure SQL Database hoặc Azure SQL Managed Instance
- Target name/FQDN:

## Expected output
- Database option baseline
- Size baseline
- Top query baseline
- App connection pool baseline
- Risk list
```

## Lab 1.2 - Source và database option

```sql
SELECT
    @@SERVERNAME AS server_name,
    @@VERSION AS sql_version,
    SERVERPROPERTY('Edition') AS edition,
    SERVERPROPERTY('ProductVersion') AS product_version,
    SERVERPROPERTY('ProductLevel') AS product_level;

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

Pass criteria:

- Recovery model được ghi nhận.
- `page_verify_option_desc` là `CHECKSUM` hoặc có action item.
- Query Store được bật hoặc có lý do chưa bật.

## Lab 1.3 - Size và file baseline

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

## Lab 1.4 - Query Store baseline

```sql
USE SalesAppDB;
GO

ALTER DATABASE SalesAppDB SET QUERY_STORE = ON;

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

## Checklist cuối ngày 1

- [ ] Có ticket giả lập.
- [ ] Có baseline SQL Server version, edition và patch level.
- [ ] Có database option baseline.
- [ ] Có size baseline.
- [ ] Có top query baseline.
- [ ] Có HikariCP active/idle/pending/timeout baseline.
- [ ] Có danh sách rủi ro trước migration.
- [ ] Evidence không chứa secret.

## Bài kiểm tra năng lực ngày 1

Người học phải trình bày trong 5 phút:

1. App Java đi qua những lớp nào trước khi query tới database.
2. Metric nào thuộc app, metric nào thuộc database, metric nào thuộc network.
3. Baseline nào sẽ dùng để so sánh sau migration.
4. Rủi ro nào nếu migration khi chưa có baseline.

## Deliverable ngày 1 - Baseline dossier

```markdown
# Baseline Dossier - SalesAppDB

## 1. Scope
- Ticket:
- App:
- Database:
- Source instance:
- Target candidate:
- Owner:

## 2. Architecture path
- Client:
- Java service:
- JDBC/Hikari:
- Network:
- SQL endpoint:
- Database:

## 3. SQL baseline
| Area | Value | Evidence |
|---|---|---|
| Version | | |
| Edition | | |
| Compatibility level | | |
| Recovery model | | |
| Query Store | | |
| Size | | |

## 4. App baseline
| Metric | Value | Time window |
|---|---:|---|
| API p95 | | |
| API p99 | | |
| Hikari active | | |
| Hikari idle | | |
| Hikari pending | | |
| Timeout count | | |

## 5. Initial risks
| Risk | Evidence | Owner | Action |
|---|---|---|---|

## 6. Learning gaps
| Gap | Impact | Plan |
|---|---|---|
```

# Ngày 2 - IO, execution plan và index review

## Mục tiêu

Người học biết phân tích query chậm bằng logical reads, CPU, elapsed time và actual execution plan. Kết thúc ngày 2 phải có đề xuất tuning có bằng chứng, không chỉ "thêm index".

Lời giải chi tiết: [[outputs/loi-giai-ngay-2-io-execution-plan-index-review]]

## Lab 2.1 - Đo IO

```sql
USE SalesAppDB;
GO

SET STATISTICS IO ON;
SET STATISTICS TIME ON;

EXEC dbo.GetCustomerOrders @CustomerId = 12345;

SET STATISTICS IO OFF;
SET STATISTICS TIME OFF;
```

Ghi lại:

- Logical reads.
- CPU time.
- Elapsed time.
- Operator chính trong actual plan.
- Có scan, key lookup, sort, hash hoặc warning không.

## Lab 2.2 - Review index hiện có

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
JOIN sys.indexes AS i ON t.object_id = i.object_id
JOIN sys.index_columns AS ic ON i.object_id = ic.object_id AND i.index_id = ic.index_id
JOIN sys.columns AS c ON ic.object_id = c.object_id AND ic.column_id = c.column_id
WHERE t.name IN ('Orders', 'Customers', 'PaymentEvents')
ORDER BY t.name, i.index_id, ic.key_ordinal, ic.index_column_id;
```

## Lab 2.3 - Tạo change record cho index

Chỉ đề xuất index khi có evidence:

```sql
CREATE INDEX IX_Orders_CustomerId_CreatedAt
ON dbo.Orders (CustomerId, CreatedAt DESC)
INCLUDE (OrderStatus, TotalAmount);
```

Change record tối thiểu:

| Trường | Nội dung |
|---|---|
| Lý do | Query nào, ảnh hưởng gì |
| Evidence trước | Reads, CPU, elapsed, plan |
| Rủi ro | Write overhead, storage, plan change |
| Test result | Before/after |
| Rollback | `DROP INDEX IX_Orders_CustomerId_CreatedAt ON dbo.Orders;` |
| Post-check | Query Store, app smoke test |

## Checklist cuối ngày 2

- [ ] Có before/after metric.
- [ ] Có actual execution plan hoặc plan hash.
- [ ] Có giải thích vì sao index giúp query.
- [ ] Có rủi ro write overhead.
- [ ] Có rollback script.
- [ ] Có quyết định: apply, defer hoặc reject.

## Bài kiểm tra năng lực ngày 2

Người học phải giải thích được:

- Logical reads khác physical reads như thế nào.
- Vì sao query trả về ít dòng vẫn có thể đọc rất nhiều page.
- Key lookup khi nào chấp nhận được, khi nào nguy hiểm.
- Missing index suggestion vì sao không được áp dụng mù quáng.
- Index mới ảnh hưởng insert/update/delete, storage và buffer pool ra sao.

## Deliverable ngày 2 - Query tuning dossier

```markdown
# Query Tuning Dossier

## Query identity
- Procedure/query:
- Query Store query_id:
- Plan id/hash:
- Business endpoint:

## Before
| Metric | Value |
|---|---:|
| Logical reads | |
| CPU ms | |
| Elapsed ms | |
| Executions/hour | |

## Plan observations
- Main operators:
- Estimated vs actual row mismatch:
- Warnings:
- Missing/duplicate/overlapping index:

## Options considered
| Option | Benefit | Risk | Decision |
|---|---|---|---|

## After test
| Metric | Before | After | Delta |
|---|---:|---:|---:|

## Production change note
- Risk level:
- Rollback:
- Post-change monitoring:
```

# Ngày 3 - Transaction, blocking và deadlock

## Mục tiêu

Người học biết tìm blocker, hiểu rủi ro kill session và liên hệ được transaction dài với API timeout trong Java.

Lời giải chi tiết: [[outputs/loi-giai-ngay-3-blocking-deadlock-transaction]]

## Lab 3.1 - Tạo blocking có kiểm soát

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

## Lab 3.2 - Quyết định xử lý blocking

Không kill ngay. Điền bảng:

| Câu hỏi | Trả lời |
|---|---|
| Head blocker là session nào? | |
| Login/program/host là gì? | |
| Transaction mở bao lâu? | |
| Query đang làm gì? | |
| Có đang rollback không? | |
| Impact với app là gì? | |
| Mitigation an toàn nhất là gì? | |
| Ai được quyền quyết định kill? | |

## Lab 3.3 - RCA mini report

```markdown
# Blocking RCA Mini Report

## Summary

## Timeline

## Impact

## Evidence
- Blocking chain:
- SQL text:
- App timeout/log:

## Root cause

## Corrective action

## Preventive action
```

## Checklist cuối ngày 3

- [ ] Tìm được blocker và blocked session.
- [ ] Giải thích được wait type.
- [ ] Biết vì sao transaction dài gây timeout.
- [ ] Có quyết định mitigation có owner.
- [ ] Có RCA mini report.

## Bài kiểm tra năng lực ngày 3

Người học phải xử lý tình huống giả lập:

```text
Checkout API timeout 30 giây.
Hikari pending tăng.
SQL có 18 session bị block bởi một session đang UPDATE bảng Orders.
Application owner hỏi DBA có nên kill session không.
```

Câu trả lời đạt chuẩn phải có:

- Evidence cần thu trước khi quyết định.
- Rủi ro nếu kill.
- Ai là người approve.
- Mitigation tạm thời.
- Preventive action ở app hoặc database.

## Deliverable ngày 3 - Locking incident dossier

```markdown
# Locking Incident Dossier

## Incident context
- Severity:
- Impacted API:
- Start time:
- Detected by:

## Blocking chain
| Blocked session | Blocking session | Wait type | Wait seconds | SQL text summary |
|---|---|---|---:|---|

## Head blocker
- Login:
- Host:
- Program:
- Open transaction age:
- Last request:
- Current status:

## Decision
- Kill? Yes/No:
- Approver:
- Reason:
- Risk:

## App correlation
- Hikari pending:
- API timeout:
- Error rate:

## Prevention
- Transaction scope change:
- Index/query change:
- Alert/SOP change:
```

# Ngày 4 - Query Store, plan regression và release safety

## Mục tiêu

Người học dùng Query Store để tìm top query, so sánh plan, phát hiện regression và đưa ra quyết định mitigation.

Lời giải chi tiết: [[outputs/loi-giai-ngay-4-query-store-regression-release-safety]]

## Lab 4.1 - Top query theo duration/CPU/reads

```sql
SELECT TOP (20)
    q.query_id,
    p.plan_id,
    qt.query_sql_text,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_logical_io_reads,
    rs.count_executions,
    rs.last_execution_time
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q ON qt.query_text_id = q.query_text_id
JOIN sys.query_store_plan AS p ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats AS rs ON p.plan_id = rs.plan_id
ORDER BY rs.avg_duration DESC;
```

## Lab 4.2 - Release safety checklist

Trước khi approve database-touching release:

- [ ] Query mới hoặc query đổi logic đã có plan review.
- [ ] DDL script có rollback.
- [ ] Data migration script có batch size và retry strategy.
- [ ] Có risk về lock/log growth/replication lag.
- [ ] Có post-change monitoring ít nhất 30 phút.
- [ ] Có owner app sẵn sàng smoke test.

## Lab 4.3 - Mitigation decision

Nếu plan regression xảy ra, lựa chọn phải có lý do:

| Option | Khi dùng | Rủi ro |
|---|---|---|
| Force plan cũ | Plan cũ ổn định, regression rõ | Plan cũ có thể không hợp khi data đổi |
| Update statistics | Estimate lệch do statistics cũ | Có thể đổi plan nhiều query |
| Recompile | Parameter sniffing cục bộ | Tăng compile CPU |
| Index hoặc rewrite | Có bằng chứng IO/plan rõ | Cần change process |
| Rollback release | Regression do code mới | Cần app owner/CAB |

## Checklist cuối ngày 4

- [ ] Tìm được query_id và plan_id.
- [ ] So sánh được plan tốt/xấu.
- [ ] Có mitigation decision.
- [ ] Có post-change monitoring checklist.
- [ ] Có ghi evidence cho release safety.

## Bài kiểm tra năng lực ngày 4

Người học phải đọc một regression report và đưa ra quyết định:

| Evidence | Ý nghĩa cần phân tích |
|---|---|
| Avg duration tăng 5 lần | Có thể là plan regression, blocking hoặc resource saturation. |
| Logical reads tăng 20 lần | Có thể do plan đổi sang scan/lookup nhiều. |
| CPU không tăng nhưng elapsed tăng | Có thể là lock, IO, network hoặc worker/session wait. |
| Query Store có plan cũ tốt hơn | Có thể cân nhắc force plan tạm thời. |
| Deploy xảy ra trước khi lỗi bắt đầu | Cần liên hệ change/release timeline. |

## Deliverable ngày 4 - Regression dossier

```markdown
# Query Store Regression Dossier

## Time window
- Before:
- After:
- Related deployment/change:

## Query Store evidence
| Query ID | Plan ID | Avg duration | Avg CPU | Avg reads | Executions |
|---|---:|---:|---:|---:|---:|

## Plan comparison
| Aspect | Good plan | Bad plan |
|---|---|---|
| Main access path | | |
| Join strategy | | |
| Estimated rows | | |
| Actual rows | | |
| Warnings | | |

## Mitigation decision
- Chosen option:
- Why:
- Risk:
- Rollback:
- Monitoring window:
```

# Ngày 5 - Backup/restore Azure SQL Database và Managed Instance

## Mục tiêu

Người học hiểu backup/restore ở mức junior DBA cho Azure SQL Database và Azure SQL Managed Instance. Kết quả ngày 5 phải chứng minh được restore bằng evidence: recovery point, restore target, row count, object count, security/app validation, RPO/RTO và cleanup.

Lời giải chi tiết: [[outputs/loi-giai-ngay-5-backup-restore-azure-sql]]

## Track backup/restore bắt buộc

| Track | Khi chọn | Evidence bắt buộc |
|---|---|---|
| Track A - Azure SQL Database | Test automated backup/PITR cho database-level PaaS. | Restore point, restored database, contained user/app validation, cleanup. |
| Track B - Azure SQL Managed Instance | Test restore/PITR hoặc restore validation cho instance-level PaaS. | MI name, restored database, login-user mapping, app validation, cleanup. |
| Cả hai | Muốn học đầy đủ. | Hai restore dossier riêng, không trộn evidence. |

## Lab 5.1 - Restore scope

```markdown
# LAB-D5 - Backup Restore Validation

## Scope
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Source database:
- Restore database:
- Restore point:
- Operator:
- Owner:
```

## Lab 5.2 - Pre-restore evidence

```sql
SELECT
    DB_NAME() AS database_name,
    SYSUTCDATETIME() AS captured_at_utc;

SELECT
    type_desc,
    COUNT(*) AS object_count
FROM sys.objects
WHERE is_ms_shipped = 0
GROUP BY type_desc
ORDER BY type_desc;
```

Nếu có bảng lab:

```sql
SELECT COUNT_BIG(*) AS customers_count FROM dbo.Customers;
SELECT COUNT_BIG(*) AS orders_count FROM dbo.Orders;
```

## Lab 5.3 - Restore validation matrix

| Check | Expected | Actual | Status |
|---|---|---|---|
| Database online | Online | | |
| Row count | Match restore point | | |
| Object count | Match source baseline | | |
| App login/user | Pass | | |
| App smoke test | Pass | | |
| RPO | Within target | | |
| RTO | Within target | | |
| Cleanup | Planned/done | | |

## Lab 5.4 - Thực hành PITR cho Azure SQL Database

Mục tiêu của bài này là restore database sang một database mới, không ghi đè database nguồn.

```markdown
# LAB-D5-A - Azure SQL Database PITR

## Input
- Source database:
- Restore database: <source>_RestoreTest_YYYYMMDD
- Restore point UTC:
- Expected RPO:
- Expected RTO:

## Execution note
- Restore start:
- Restore online:
- Validation pass:
- Cleanup time:
```

Các bước thực hành:

1. Vào Azure Portal, mở source database.
2. Kiểm tra backup retention và restore point có thể chọn.
3. Chọn **Restore** và đặt tên database restore test.
4. Chờ database restore online.
5. Kết nối vào database restore test và chạy validation.
6. Ghi RPO/RTO thực tế.
7. Xóa database restore test sau khi đã lưu evidence, nếu đây chỉ là lab.

Validation tối thiểu:

```sql
SELECT
    DB_NAME() AS database_name,
    SYSUTCDATETIME() AS validated_at_utc;

SELECT
    type_desc,
    COUNT(*) AS object_count
FROM sys.objects
WHERE is_ms_shipped = 0
GROUP BY type_desc
ORDER BY type_desc;
```

Nếu database lab có bảng nghiệp vụ, thêm kiểm tra row count:

```sql
SELECT COUNT_BIG(*) AS customers_count FROM dbo.Customers;
SELECT COUNT_BIG(*) AS orders_count FROM dbo.Orders;
```

Kết luận phải ghi rõ: restore pass/fail, restore point có đạt RPO không, validation có đạt không, app/user có kết nối được không.

## Lab 5.5 - Thực hành restore validation cho Managed Instance

Mục tiêu của bài này là kiểm tra restore/PITR trong Azure SQL Managed Instance và xác nhận phần quyền đăng nhập không bị bỏ sót.

```markdown
# LAB-D5-B - Managed Instance Restore Validation

## Input
- Managed instance:
- Source database:
- Restore database:
- Restore mode: PITR / native backup / DMS rehearsal
- Restore point or backup file:
- App test host:
```

Validation trên database restore:

```sql
SELECT
    DB_NAME() AS database_name,
    SUSER_SNAME() AS login_name,
    USER_NAME() AS database_user,
    SYSUTCDATETIME() AS validated_at_utc;

SELECT
    name,
    state_desc,
    compatibility_level
FROM sys.databases
WHERE name = DB_NAME();

SELECT
    dp.name AS database_user,
    dp.type_desc,
    dp.authentication_type_desc
FROM sys.database_principals AS dp
WHERE dp.name NOT IN ('dbo', 'guest', 'INFORMATION_SCHEMA', 'sys');
```

Pass khi database online, dữ liệu/schema hợp lý, login-user mapping rõ, app smoke test chạy được từ network hợp lệ, và restore duration đã được ghi lại.

## Lab 5.6 - Evidence restore cuối bài

Người học nộp một dossier ngắn:

```markdown
# Restore Evidence

## Result
- Pass/Fail:
- RPO actual:
- RTO actual:
- Restore database:
- Cleanup status:

## Evidence
- Retention/restore point:
- Pre-restore object/row count:
- Post-restore object/row count:
- Security/app validation:
- Issue found:
- Lesson learned:
```

## Checklist cuối ngày 5

- [ ] Biết retention/restore point.
- [ ] Restore sang database mới hoặc môi trường test.
- [ ] Có row/object validation.
- [ ] Có security/app validation.
- [ ] Có RPO/RTO thực tế.
- [ ] Có cleanup plan.
- [ ] Không nhầm backup success với restore readiness.

## Bài kiểm tra năng lực ngày 5

Người học phải trả lời:

- Backup success khác restore readiness thế nào?
- PITR của Azure SQL Database/Managed Instance dùng để xử lý tình huống nào?
- RPO/RTO đo thế nào trong restore test?
- Restore xong nhưng app login fail thì validation thiếu gì?
- Vì sao phải cleanup database restore test?

## Deliverable ngày 5 - Restore validation dossier

```markdown
# Restore Validation Dossier

## Scope
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Source database:
- Restore database:
- Restore point:
- Operator:
- Owner:

## Restore execution
| Step | Start | End | Status | Evidence |
|---|---|---|---|---|
| Pre-check | | | | |
| Restore started | | | | |
| Database online | | | | |
| Validation | | | | |
| Cleanup | | | | |

## Validation
| Check | Expected | Actual | Status |
|---|---|---|---|
| Row count | | | |
| Object count | | | |
| App login/user | | | |
| App smoke test | | | |
| RPO | | | |
| RTO | | | |

## Decision
- Pass/Fail:
- Risk remaining:
- Lesson learned:
- Action owner:
```

# Ngày 6 - Đọc metric và monitoring Azure SQL

## Mục tiêu

Người học biết đọc hiện trạng Azure SQL Database hoặc Managed Instance bằng Azure metrics, Query Store, DMV và app metrics. Kết quả ngày 6 là một health report có time window, metric interpretation, Query Store analysis, active request/blocking check, app correlation và quyết định vận hành.

Lời giải chi tiết: [[outputs/loi-giai-ngay-6-doc-metric-va-theo-doi-azure-sql]]

## Lab 6.1 - Health snapshot

```markdown
# Azure SQL Health Snapshot

## Scope
- Target type:
- Target name:
- Database:
- Time window:
- Timezone:

## Platform metrics
| Metric | Value | Finding |
|---|---:|---|
| CPU % | | |
| Data IO % | | |
| Log IO % | | |
| Sessions % | | |
| Workers % | | |
| Storage used | | |
```

## Lab 6.2 - Query Store và active request

Người học phải lấy:

- Top query theo duration.
- Top query theo CPU/reads nếu có.
- Active requests.
- Blocking hiện tại.
- App correlation: Hikari active/idle/pending/timeout, API p95/p99.

Query Store mẫu:

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

Active request mẫu:

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

## Lab 6.3 - Đọc resource stats

Với Azure SQL Database, người học chạy:

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

Ghi nhận xét theo mẫu:

```markdown
## Metric interpretation
- CPU: normal/high, evidence:
- Data IO: normal/high, evidence:
- Log IO: normal/high, evidence:
- Worker/session: normal/high, evidence:
- Main hypothesis:
- Next action:
```

## Lab 6.4 - Monitoring drill theo tình huống

Chọn một tình huống và viết kết luận vận hành:

| Tình huống | Evidence phải tìm | Quyết định mong đợi |
|---|---|---|
| CPU cao | Top CPU query, execution count, plan/change gần nhất. | Tune query/index hoặc scale tạm nếu cần giảm nóng. |
| Data IO cao | Logical reads, scans, missing/unused index, Query Store. | Review plan/index/statistics. |
| Log IO cao | Giao dịch ghi lớn, bulk load, index maintenance, retry storm. | Giảm write burst hoặc đổi lịch job. |
| Sessions/workers cao | Active sessions, app pool pending, retry, long-running query. | Kiểm tra app pool/concurrency và DB waits. |
| Duration cao CPU thấp | Wait type, blocking, IO wait, network/app correlation. | Tìm wait/root cause trước khi scale. |

Kết luận cuối bài phải là một trong bốn trạng thái: `no action`, `investigate`, `mitigate`, `escalate`.

## Checklist cuối ngày 6

- [ ] Có health snapshot có time window.
- [ ] Đọc được CPU/Data IO/Log IO/session/worker.
- [ ] Có Query Store evidence.
- [ ] Có active request/blocking check.
- [ ] Có app correlation.
- [ ] Có quyết định: no action, investigate, mitigate hoặc escalate.

## Bài kiểm tra năng lực ngày 6

Người học phải trả lời:

- CPU cao thì tìm evidence nào tiếp?
- Data IO cao khác Log IO cao thế nào?
- Sessions/workers gần giới hạn liên quan gì đến HikariCP?
- Duration cao nhưng CPU thấp gợi ý điều gì?
- Khi nào scale chỉ là mitigation tạm, không phải root cause fix?

## Deliverable ngày 6 - Azure SQL health report

```markdown
# Azure SQL Health Report

## Scope
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Target:
- Database:
- Time window:
- Timezone:
- Trigger: scheduled check / alert / user complaint / post-change

## Platform metrics
| Metric | Value | Finding | Action |
|---|---:|---|---|
| CPU % | | | |
| Data IO % | | | |
| Log IO % | | | |
| Sessions % | | | |
| Workers % | | | |
| Storage used | | | |

## Workload evidence
| Evidence | Finding |
|---|---|
| Query Store top duration | |
| Query Store top CPU/reads | |
| Active requests | |
| Blocking | |

## App correlation
- API p95/p99:
- Hikari active/idle/pending:
- Timeout/error rate:

## Decision
- Status: healthy / watch / degraded / incident
- Decision: no action / investigate / mitigate / escalate
- Owner:
- Next check:
```

# Ngày 7 - Readiness, migration rehearsal và demo cutover

## Mục tiêu

Người học gom các năng lực ngày 0-6 để làm readiness, migration rehearsal và demo cutover. Ngày 7 phải có target decision rõ, connectivity/security readiness, migration run sheet, validation report, monitoring sau cutover, rollback plan và RCA nếu có lỗi.

Lời giải chi tiết: [[outputs/loi-giai-ngay-7-readiness-migration-demo-cutover]]

## Lab 7.1 - Target readiness

```markdown
# Azure SQL Readiness Dossier

## Target choice
- Option: Azure SQL Database / Azure SQL Managed Instance / SQL Server on Azure VM
- Reason:
- Trade-off:

## Network
- Endpoint/FQDN:
- Firewall/private endpoint/VNet/subnet:
- DNS:
- App path:

## Go/No-Go
- Decision:
- Conditions:
```

## Lab 7.2 - Migration rehearsal

Ngày 7 dùng cả:

- Restore evidence từ ngày 5.
- Metric/monitoring baseline từ ngày 6.
- Target readiness ở lab 7.1.
- Migration rehearsal và validation.

## Lab 7.3 - Demo cutover

## Cấu trúc demo 60-90 phút

| Phần | Thời lượng | Nội dung |
|---|---:|---|
| Context | 5 phút | Source, target, app, scope, risk |
| Baseline | 10 phút | Version, DB size, top query, app metrics |
| Assessment | 10 phút | Blocker, warning, remediation, go/no-go |
| Migration execution | 15-25 phút | Chạy hoặc replay theo run sheet |
| Validation | 15 phút | Data, schema, security, app smoke test |
| Monitoring | 10 phút | CPU/IO/session/query/blocking/app metric |
| Decision | 10 phút | Go/no-go, rollback trigger, known issues, next action |

## Post-migration monitoring queries

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

Query Store sau migration:

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

## RCA mẫu nếu demo gặp lỗi

```markdown
# Incident RCA - Migration Demo

## Summary

## Timeline
| Time | Event |
|---|---|

## Impact

## Root Cause

## Evidence
- Error:
- Metric:
- Query/log:

## Corrective Action

## Preventive Action

## Lessons Learned
```

## Deliverable ngày 7 - Final demo dossier

```markdown
# Final Demo Dossier - Junior Azure SQL DBA

## Scope
- App:
- Source:
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Target:
- Database:
- Migration mode:

## Inputs from previous days
| Input | Source day | Evidence |
|---|---:|---|
| Target provisioning | 0 | |
| Baseline | 1 | |
| Query/index risk | 2 | |
| Blocking/deadlock risk | 3 | |
| Query Store regression risk | 4 | |
| Restore/RPO/RTO | 5 | |
| Monitoring baseline | 6 | |

## Readiness
| Area | Finding | Status |
|---|---|---|
| Compatibility | | |
| Network/connectivity | | |
| Security | | |
| Capacity | | |

## Migration run sheet
| Step | Status | Evidence |
|---|---|---|
| Pre-check | | |
| Migration/rehearsal | | |
| Data validation | | |
| Security validation | | |
| App smoke test | | |
| Monitoring window | | |

## Decision
- Go/No-Go:
- Rollback trigger:
- Known issues:
- Production readiness:
- Next actions:
```

## Rubric chấm cuối khóa

| Năng lực | Chưa đạt | Đạt | Tốt |
|---|---|---|---|
| Baseline | Số liệu rời rạc | Có SQL + app baseline | Có nhận xét rủi ro và so sánh |
| Evidence | Không lưu rõ | Có evidence chính | Evidence đủ trước/trong/sau thao tác |
| Execution plan | Không đọc được | Nhận ra scan/seek/lookup | Đề xuất fix có trade-off |
| Blocking/deadlock | Không tìm blocker | Tìm và giải thích được | Có RCA và preventive action |
| Query Store | Chỉ xem top query | So sánh plan/regression | Có mitigation decision hợp lý |
| Azure SQL target | Mô tả mơ hồ, không phân biệt Database/MI | Có target design đúng loại | Gắn được network/security/cost/monitoring theo từng loại |
| Change safety | Không có rollback | Có rollback plan | Có trigger, owner, post-monitoring |
| Migration | Chỉ copy/restore | Có run sheet + validation | Có rollback, monitoring, go/no-go |
| App smoke test | Không test app | Test read/write | Có HikariCP và API metric |
| Communication | Nói rời rạc | Có timeline và decision | Có risk, owner, action rõ |

## Câu hỏi vấn đáp cuối khóa

Mentor nên hỏi tối thiểu các câu sau:

1. Em dựa vào evidence nào để nói query chậm do IO, CPU, lock hay app pool?
2. Nếu tạo index này, write workload, storage và plan stability bị ảnh hưởng thế nào?
3. Nếu session đang rollback, kill thêm có giúp không?
4. Nếu Query Store có plan cũ tốt hơn, khi nào nên force plan và khi nào không?
5. Nếu app timeout nhưng SQL query nhanh, em kiểm tra gì ở HikariCP và network?
6. Nếu migration xong nhưng app login fail, em kiểm tra login, user, role, default schema và auth mode thế nào?
7. Nếu target Azure SQL Database hoặc Managed Instance chậm hơn source, em phân biệt cold cache, resource tier, statistics và plan regression thế nào?
8. Nếu private endpoint vừa bật xong và app không kết nối được, em kiểm tra DNS và route như thế nào?
9. Nếu rollback sau cutover, dữ liệu ghi mới sau cutover xử lý ra sao?
10. Evidence nào bắt buộc phải có trước khi đóng ticket migration?

## Phụ lục A - Checklist query chậm

- [ ] Xác định query/procedure/API endpoint.
- [ ] Xác định thời điểm bắt đầu chậm và tần suất.
- [ ] Lấy Query Store query_id, plan_id.
- [ ] Ghi avg duration, avg CPU, avg logical reads, executions.
- [ ] Chạy `STATISTICS IO/TIME` trên môi trường phù hợp.
- [ ] Mở actual execution plan.
- [ ] Kiểm tra scan/seek/key lookup/sort/hash/spill/implicit conversion.
- [ ] So sánh estimated rows và actual rows.
- [ ] Kiểm tra blocking trong lúc query chạy.
- [ ] Kiểm tra wait type.
- [ ] Kiểm tra statistics và parameter sensitivity.
- [ ] Kiểm tra release/change gần nhất.
- [ ] Kiểm tra HikariCP pending/timeout nếu app báo chậm.
- [ ] Đề xuất ít nhất hai option, không chỉ một option.
- [ ] Ghi risk và rollback nếu có thay đổi.

## Phụ lục B - Checklist API timeout

- [ ] API nào timeout.
- [ ] Timeout ở app, gateway, JDBC hay database.
- [ ] Error message đã che secret.
- [ ] Hikari active/idle/pending/timeout.
- [ ] SQL active requests.
- [ ] Blocking chain.
- [ ] Wait type.
- [ ] Query text hoặc stored procedure.
- [ ] Connection/session/worker saturation.
- [ ] Azure CPU/data IO/log IO.
- [ ] Network/DNS/private endpoint/firewall nếu lỗi connect.
- [ ] Deployment hoặc config change gần nhất.
- [ ] Mitigation tạm thời.
- [ ] RCA hoặc problem ticket nếu lặp lại.

## Phụ lục C - Checklist Azure SQL Database / Managed Instance security

- [ ] App không dùng admin login.
- [ ] App principal có quyền tối thiểu.
- [ ] Privileged access có approval và expiry.
- [ ] Audit bật theo policy.
- [ ] TDE bật.
- [ ] Backup retention phù hợp.
- [ ] Defender/vulnerability assessment được review nếu tổ chức dùng.
- [ ] Private endpoint hoặc firewall rule được quản lý.
- [ ] Không có IP mở rộng không cần thiết.
- [ ] Secrets lưu trong vault/config store được kiểm soát.
- [ ] Evidence không chứa secret.

## Phụ lục D - Checklist migration production-like

- [ ] Scope được app owner xác nhận.
- [ ] Baseline đã có.
- [ ] Assessment đã có.
- [ ] Blocker đã xử lý.
- [ ] Target sizing được review.
- [ ] Network path đã test từ app.
- [ ] Login/user/role đã map.
- [ ] Backup/restore point đã verify.
- [ ] Run sheet đã review.
- [ ] Validation script đã chuẩn bị.
- [ ] App smoke test owner có mặt.
- [ ] Monitoring dashboard sẵn sàng.
- [ ] Rollback trigger rõ.
- [ ] Rollback owner rõ.
- [ ] Data reconciliation plan có nếu có write sau cutover.
- [ ] Communication channel mở.
- [ ] Go/no-go được ghi lại.

## Phụ lục E - Checklist sau migration

- [ ] Database online.
- [ ] Row count match hoặc lệch có giải thích.
- [ ] Object count match hoặc lệch có giải thích.
- [ ] App login thành công.
- [ ] Read API pass.
- [ ] Write API pass hoặc rollback smoke test pass.
- [ ] Query Store ghi workload mới.
- [ ] Top query không regression nghiêm trọng.
- [ ] CPU/data IO/log IO trong ngưỡng.
- [ ] Không có blocking kéo dài.
- [ ] Không có deadlock spike.
- [ ] Hikari pending không tăng bất thường.
- [ ] Error rate không tăng.
- [ ] Alert mới được xử lý.
- [ ] Known issues có owner.
- [ ] Ticket/change có evidence trước khi đóng.

## Template báo cáo cuối khóa

```markdown
# Final Demo Report - Junior Azure SQL DBA

## Scope
- App:
- Source:
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Target:
- Database:
- Migration mode:

## Baseline
| Area | Evidence | Finding |
|---|---|---|

## Assessment
| Severity | Finding | Action | Owner | Status |
|---|---|---|---|---|

## Execution
| Step | Status | Evidence |
|---|---|---|

## Validation
| Check | Source | Target | Status |
|---|---|---|---|

## Monitoring
| Metric | Before | After | Status |
|---|---:|---:|---|

## Decision
- Go/No-Go:
- Known issues:
- Rollback trigger:
- Next actions:
```

## Tài liệu tham chiếu trong wiki

- [[sources/master-dba-runbook-backend-java-azure-sql]]: nền tảng thực hành dev-to-DBA, evidence-first, Query Store, blocking/deadlock, RCA.
- [[sources/dba-operations-framework]]: service catalog, operating model, RACI, policy, SOP, runbook, template, KPI và evidence.
- [[sources/sql-server-2022-administration-inside-out]]: nền tảng quản trị SQL Server, backup/restore, HA/DR, security và performance.
- [[sources/azure-sql-documentation]]: Azure SQL Database/Managed Instance, monitoring, security và vận hành cloud.
- [[sources/azure-database-migration-guides]]: discovery, assessment, migration, cutover và validation.
- [[sources/azure-database-migration-service-documentation]]: Azure Database Migration Service và migration online/offline.
- [[sources/runbook-backup-verification]] và [[sources/runbook-restore-validation]]: kiểm chứng backup/restore.
- [[sources/runbook-database-health-check]] và [[sources/runbook-capacity-report]]: health check và capacity evidence.

## Bộ lời giải theo ngày

Bộ 8 lời giải này không thay thế runbook chính. Cách dùng đúng là:

1. Dùng runbook chính để biết **phải làm gì, nộp gì, đạt hay chưa đạt**.
2. Mở lời giải theo ngày để xem **cách làm kỹ lưỡng, tình huống thực tế, lỗi thường gặp và tiêu chí chấm sâu**.
3. Đem evidence của ngày trước sang ngày sau; không làm mỗi ngày như một bài rời rạc.

| Ngày | Lời giải | Khi dùng |
|---|---|---|
| 0 | [[outputs/loi-giai-ngay-0-tao-azure-sql-database-moi-tren-cloud]] | Khi cần tạo target lab Azure SQL Database hoặc Managed Instance có connection/security/cleanup rõ. |
| 1 | [[outputs/loi-giai-ngay-1-baseline-va-operating-model]] | Khi cần hiểu operating model, service request, baseline SQL/app và risk list. |
| 2 | [[outputs/loi-giai-ngay-2-io-execution-plan-index-review]] | Khi cần phân tích IO, execution plan, index trade-off và change record. |
| 3 | [[outputs/loi-giai-ngay-3-blocking-deadlock-transaction]] | Khi cần xử lý blocking/deadlock, transaction dài, app timeout và RCA mini. |
| 4 | [[outputs/loi-giai-ngay-4-query-store-regression-release-safety]] | Khi cần dùng Query Store để phát hiện regression và quyết định mitigation. |
| 5 | [[outputs/loi-giai-ngay-5-backup-restore-azure-sql]] | Khi cần thực hành PITR/restore validation, RPO/RTO và cleanup. |
| 6 | [[outputs/loi-giai-ngay-6-doc-metric-va-theo-doi-azure-sql]] | Khi cần đọc Azure metric, DMV, Query Store và app correlation. |
| 7 | [[outputs/loi-giai-ngay-7-readiness-migration-demo-cutover]] | Khi cần tổng hợp readiness, migration rehearsal, demo cutover, monitoring, rollback và RCA. |

## Luồng evidence cuối khóa

```text
Target provisioning dossier
  + Baseline dossier
  + Query tuning dossier
  + Locking incident dossier
  + Regression dossier
  + Restore validation dossier
  + Azure SQL health report
  = Final demo dossier
```

Nếu thiếu một dossier ở giữa, ngày 7 vẫn có thể demo kỹ thuật nhưng chưa đạt chuẩn vận hành.

## Kết luận

Junior Azure SQL DBA tốt không phải người nhớ nhiều câu lệnh nhất. Người đạt chuẩn là người biết đặt thao tác vào đúng service, thu thập đúng evidence, hiểu rủi ro trước khi thay đổi, validate sau khi làm và giao tiếp rõ ràng khi hệ thống đang chịu áp lực.
