---
id: phu-luc-van-hanh-azure-sql-hoan-thien
title: "Phụ lục vận hành Azure SQL - bản hoàn thiện đề xuất"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - sources/azure-sql-operations-appendix
  - sources/azure-sql-documentation
  - sources/sql-server-2022-administration-inside-out
  - sources/dba-operations-framework
  - concepts/azure-sql
  - concepts/azure-sql-database
  - concepts/azure-sql-managed-instance
  - concepts/azure-sql-monitoring-and-observability
---

# APP-060 - Azure SQL Operations Appendix

## 1. Mục đích

Phụ lục này đặc tả cách áp dụng DBA Operations Framework cho Azure SQL Database và Azure SQL Managed Instance. Nội dung giúp DBA chuyển các kiểm soát truyền thống như backup, restore, HA/DR, monitoring, performance, security, access control, capacity và evidence sang mô hình PaaS.

## 2. Phạm vi áp dụng

Áp dụng cho Azure SQL Database, elastic pool, Hyperscale, serverless nếu được sử dụng, và Azure SQL Managed Instance. Với SQL Server cài trên Azure VM, sử dụng APP-056 vì trách nhiệm vận hành vẫn gần với SQL Server truyền thống.

## 3. Nguyên tắc vận hành Azure SQL

1. PaaS giảm việc quản trị hạ tầng nhưng không loại bỏ trách nhiệm DBA về dữ liệu, cấu hình, bảo mật, hiệu năng, chi phí và evidence.
2. Backup tự động không thay thế restore drill và validation.
3. Failover group hoặc geo-replication chỉ có giá trị khi application connection strategy đã được thiết kế và kiểm thử.
4. Diagnostic settings, Azure Monitor, Query Store và alert rule phải được bật theo chuẩn trước khi bàn giao production.
5. Mọi exception về public access, firewall, private endpoint, LTR, retention, alert hoặc access control cần owner và approval.

## 4. Khác biệt vận hành theo service model

| Chủ đề | Azure SQL Database | Azure SQL Managed Instance |
|---|---|---|
| Phạm vi quản trị | Database-level PaaS | Instance-level PaaS gần SQL Server hơn |
| Job scheduling | Elastic Jobs, Automation, Function, Logic Apps, Data Factory | SQL Agent, Automation hoặc external scheduler |
| Networking | Logical server firewall, private endpoint | VNet integration, private endpoint theo thiết kế |
| HA | Built-in HA theo tier, zone redundancy nếu cấu hình | Built-in HA, zone redundancy nếu cấu hình |
| DR | Geo-restore, active geo-replication, failover group | Failover group, geo-restore |
| Performance | DTU/vCore, elastic pool, Query Store, automatic tuning | vCore, Query Store, wait stats, gần SQL Server hơn |
| Migration fit | App cloud-native hoặc database độc lập | Migration SQL Server cần compatibility cao |

## 5. Backup, PITR và Long-Term Retention

1. Xác nhận PITR retention cho từng database hoặc managed instance theo policy.
2. Cấu hình Long-Term Retention nếu có yêu cầu lưu trữ dài hạn, audit hoặc compliance.
3. Restore drill phải chạy sang target test hoặc DR phù hợp, có recovery point, restore duration và validation report.
4. Backup verification trong Azure SQL nên kiểm tra policy, retention, restore capability, LTR state và alert liên quan thay vì chỉ nhìn job backup.
5. Evidence phải ghi rõ source, target, restore point, start/end time, người thực hiện và kết quả validation.

## 6. HA/DR và failover

1. Với workload cần DR, thiết kế failover group hoặc geo-replication cùng với endpoint mà ứng dụng sẽ dùng.
2. Planned failover phải đi qua RFC; emergency failover phải có emergency change và post-review.
3. Sau failover cần kiểm tra primary role, replication state, connectivity, firewall/private endpoint, login/user, application smoke test và monitoring.
4. Đo RTO/RPO thực tế trong drill, không chỉ ghi theo cam kết thiết kế.
5. Nếu dùng private endpoint, cần kiểm tra DNS resolution và network path ở cả primary và secondary region.

## 7. Security và access control

| Hạng mục | Kiểm soát |
|---|---|
| Identity | Ưu tiên Microsoft Entra ID khi phù hợp, quản lý SQL login theo exception |
| Network | Tắt public access khi policy yêu cầu, dùng private endpoint/firewall chuẩn |
| Encryption | TDE là baseline; kiểm tra yêu cầu customer-managed key nếu có |
| Permission | Role chuẩn, approval, evidence trước/sau thay đổi |
| Audit | Bật audit/diagnostic log theo yêu cầu compliance |
| Defender/Threat | Kiểm tra Microsoft Defender for SQL nếu policy yêu cầu |

## 8. Monitoring và observability

1. Bật diagnostic settings gửi metric/log tới Log Analytics, Event Hub hoặc storage theo chuẩn.
2. Theo dõi CPU, data IO, log IO, storage, workers/sessions, deadlock, blocking, connection failure, DTU/vCore và elastic pool utilization.
3. Query Store dùng để phân tích plan regression, top query, wait category và thay đổi sau deploy.
4. Alert rule phải map với SOP: backup/restore, capacity, performance, blocking/deadlock, failover, security và connectivity.
5. Dashboard cần phân biệt database-level, elastic pool-level, managed instance-level và application-level signal.

## 9. Performance và tuning

1. Với Azure SQL Database, kiểm tra service tier, compute size, elastic pool pressure, storage và workload pattern trước khi tuning query.
2. Với Managed Instance, áp dụng tư duy SQL Server nhưng vẫn tính đến giới hạn PaaS, storage, network và maintenance window của nền tảng.
3. Automatic tuning nếu bật phải có review định kỳ và bằng chứng tác động.
4. Không áp dụng index recommendation tự động nếu chưa xem Query Store, logical reads, write overhead và duplicate/unused indexes.
5. Khi tăng vCore/DTU hoặc chuyển tier, cần ghi lý do, thời gian, cost impact và tiêu chí rollback.

## 10. Capacity và cost

| Nhóm | Kiểm tra |
|---|---|
| Storage | Used data size, allocated size, max size, days to threshold |
| Compute | CPU/DTU/vCore trend, peak, sustained pressure |
| Log IO | Log write pressure, transaction pattern |
| Elastic pool | Pool utilization, noisy database, per-db cap |
| Hyperscale | Data growth, page server/storage behavior, restore expectation |
| Cost | SKU, reserved capacity nếu có, idle/serverless behavior |

Capacity report cần tạo action item khi forecast vượt ngưỡng hoặc khi chi phí tăng không khớp workload.

## 11. SQL Agent alternative

| Nhu cầu | Azure SQL Database | Azure SQL Managed Instance |
|---|---|---|
| Job định kỳ | Elastic Jobs, Azure Automation, Functions, Logic Apps, Data Factory | SQL Agent hoặc Automation |
| Maintenance script | External automation, built-in capability, pipeline | SQL Agent job, Automation hoặc pipeline |
| Report/evidence | Pipeline artifact, Log Analytics, ticket attachment | SQL Agent output, Log Analytics, ticket |
| Credential | Managed identity/service principal khi phù hợp | SQL credential, managed identity hoặc service principal |

Mọi automation thay thế SQL Agent vẫn cần owner, change control, log output, retry policy và alert khi thất bại.

## 12. Checklist vận hành Azure SQL

| Hạng mục | Kiểm tra |
|---|---|
| Backup | PITR retention, LTR, restore drill |
| DR | Failover group, geo-replication, geo-restore readiness |
| Security | Entra ID, firewall, private endpoint, TDE, audit |
| Monitoring | Azure Monitor, diagnostic settings, alert rule |
| Performance | Query Store, top query, waits, automatic tuning |
| Capacity | Storage, vCore/DTU, elastic pool, forecast |
| Cost | SKU, utilization, reserved/serverless fit |
| Evidence | Ticket, dashboard/export, restore/failover validation |

## 13. Điều kiện exception

Exception bắt buộc nếu database production không có diagnostic settings, không có alert critical, không có restore drill, không cấu hình LTR dù policy yêu cầu, public endpoint mở ngoài chuẩn, thiếu private endpoint theo policy, failover group chưa test hoặc automation không có evidence.

## 14. Tiêu chí nghiệm thu

Phụ lục được xem là áp dụng đúng khi Azure SQL estate có mapping service model rõ ràng, backup/restore được kiểm chứng, DR endpoint được test, monitoring và alert đã bật, security/network đúng policy, capacity/cost có forecast, automation có evidence và mọi exception có owner cùng ngày review.

