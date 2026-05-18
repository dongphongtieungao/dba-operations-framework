---
id: phu-luc-van-hanh-sql-server-hoan-thien
title: "Phụ lục vận hành SQL Server - bản hoàn thiện đề xuất"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - sources/sql-server-operations-appendix
  - sources/sql-server-2022-administration-inside-out
  - sources/sql-server-dba-toolbox
  - sources/dba-operations-framework
  - concepts/sql-server-administration
  - concepts/sql-server-configuration-baseline
  - concepts/sql-server-backup-restore-operations
  - concepts/high-availability-and-disaster-recovery
---

# APP-056 - SQL Server Operations Appendix

## 1. Mục đích

Phụ lục này đặc tả cách áp dụng DBA Operations Framework cho SQL Server. Nội dung dùng để chuyển policy, standard, SOP và runbook chung thành checklist, thao tác kiểm chứng, evidence và exception phù hợp với SQL Server on-premises, SQL Server on VM, Always On Availability Groups, Failover Cluster Instance, SQL Agent và backup native.

## 2. Phạm vi áp dụng

Áp dụng cho SQL Server production, staging và các môi trường non-production quan trọng. Với SQL Server chạy trên Azure VM, dùng phụ lục này kết hợp tiêu chuẩn hạ tầng Azure. Với Azure SQL Database hoặc Azure SQL Managed Instance, dùng thêm APP-060 vì trách nhiệm vận hành đã chuyển một phần sang mô hình PaaS.

## 3. Nguyên tắc vận hành

1. Policy chung vẫn là nguồn kiểm soát chính; phụ lục chỉ diễn giải cách thực hiện trên SQL Server.
2. Mọi thao tác production phải đi qua ticket, approval, evidence và rollback/fallback khi có rủi ro.
3. Không kết luận hệ thống an toàn chỉ vì job chạy thành công; phải kiểm tra khả năng phục hồi, consistency, monitoring và application impact.
4. Không áp dụng script tuning, force plan, rebuild index hàng loạt, shrink file hoặc repair option nếu chưa có review và approval.
5. Exception phải ghi rõ lý do, owner, thời hạn review và rủi ro chấp nhận.

## 4. Mapping vận hành SQL Server

| Chủ đề | Cơ chế SQL Server | Runbook/SOP liên quan | Evidence tối thiểu |
|---|---|---|---|
| Backup | Full, differential, transaction log backup, compression, encryption | Backup Verification | Backup report, backup age, chain status |
| Restore | Restore database/log, PITR, restore with move | Restore Validation | Restore log, recovery point, validation report |
| HA/DR | Always On AG, FCI, log shipping, listener | Failover Execution, Add Replica | AG health, failover timeline, RTO/RPO |
| Integrity | DBCC CHECKDB, suspect pages, msdb history | Corruption Handling | CHECKDB output, action item |
| Jobs | SQL Agent, Database Mail, operators, schedules | Health Check | Failed job count, notification evidence |
| Performance | Query Store, wait stats, execution plan, DMVs | Query Tuning Review | Top query, plan, wait evidence |
| Index/statistics | Rebuild, reorganize, update stats, fragmentation review | Indexing Standard | Change script, before/after metrics |
| Security | Logins, users, roles, sysadmin, audit, encryption | User Provisioning | Permission before/after, approval |
| Capacity | Data/log/tempdb/backup storage, autogrowth | Capacity Report | Growth trend, forecast, owner |
| Monitoring | Error log, Extended Events, DMVs, alerts | Health Check | Alert state, dashboard/export |

## 5. Baseline cấu hình

| Hạng mục | Kiểm soát đề xuất |
|---|---|
| Version/patch | Ghi nhận edition, build, CU level và support status |
| Service account | Không dùng tài khoản cá nhân; quyền theo nguyên tắc tối thiểu |
| TempDB | Số file, kích thước, autogrowth và storage phù hợp workload |
| Recovery model | FULL cho database cần PITR; SIMPLE chỉ khi policy cho phép |
| Page verification | CHECKSUM cho database production trừ khi có exception |
| Autogrowth | Dùng giá trị cố định hợp lý, tránh percent growth không kiểm soát |
| Query Store | Bật cho workload cần theo dõi regression, có retention và cleanup |
| Database owner | Chuẩn hóa owner, tránh owner cá nhân |
| Compatibility level | Theo application support matrix, không nâng tự động thiếu test |

## 6. Backup và restore

1. Full backup là nền tảng phục hồi chính; differential và log backup dùng theo RPO/RTO.
2. Transaction log backup bắt buộc với database FULL recovery cần PITR.
3. Backup verification phải kiểm tra last successful backup, backup age, chain, encryption, retention, file availability và storage capacity.
4. Restore drill phải chạy định kỳ, không chỉ kiểm tra file tồn tại.
5. Restore validation phải kiểm tra database online, object count, row count hoặc sample business query, orphan users, job cần disable và application smoke test nếu có.

## 7. HA/DR, Always On và failover

1. Theo dõi replica role, synchronization health, synchronization state, log send queue, redo queue và failover readiness.
2. Listener, DNS, firewall và application connection string phải được kiểm tra trước và sau failover.
3. Forced failover chỉ dùng khi có emergency approval và chấp nhận rủi ro mất dữ liệu.
4. Failover drill cần ghi timeline, RTO/RPO thực tế, application recovery time và stakeholder confirmation.
5. Khi thêm replica, phải kiểm tra seed data, endpoint, credential, network, lag, read-only routing nếu dùng và backup chain impact.

## 8. Integrity và corruption

1. DBCC CHECKDB phải được lập lịch theo mức độ quan trọng và khả năng chịu tải.
2. Nếu không thể chạy CHECKDB đầy đủ trên production, phải có phương án thay thế được phê duyệt như restore sang môi trường khác để kiểm tra.
3. Không dùng REPAIR_ALLOW_DATA_LOSS nếu chưa có approval rõ ràng từ DBA Lead và Service Owner.
4. Corruption finding phải được xử lý như incident, có evidence, impact assessment và recovery plan.

## 9. Performance, Query Store và index

1. Query Store là nguồn chính để so sánh plan regression, top duration, CPU, logical reads và execution count.
2. Khi force plan, phải ghi lý do, thời hạn review, tiêu chí rollback và evidence trước/sau.
3. Index recommendation phải được review theo workload, write overhead, duplicate/unused index và storage impact.
4. Không rebuild index hàng loạt nếu không có tiêu chí fragmentation, page count, maintenance window và rollback.
5. Cập nhật statistics cần cân nhắc sample, fullscan, workload và plan stability.

## 10. Security và audit

1. Kiểm tra sysadmin membership, fixed server roles, database owner, orphan users và login không còn hợp lệ.
2. Human user trên production cần approval, role chuẩn và review định kỳ hoặc expiration.
3. Service account và application account phải có owner, purpose, rotation hoặc review process.
4. Audit evidence phải thể hiện quyền trước/sau thay đổi.
5. Encryption at rest và in transit phải được kiểm tra theo policy.

## 11. Monitoring và health check

Checklist định kỳ tối thiểu:

| Nhóm | Kiểm tra |
|---|---|
| Instance | Service up, uptime, restart gần nhất, version/build |
| Jobs | Failed SQL Agent jobs, job đang chạy lâu, notification |
| Backup | Backup age, backup chain, job failure, storage |
| HA | Replica health, listener, failover readiness |
| Integrity | CHECKDB gần nhất, suspect pages, error log |
| Performance | CPU, waits, blocking, deadlock, Query Store |
| Capacity | Data/log/tempdb, autogrowth event, days to threshold |
| Security | Sysadmin, failed login, audit finding |

## 12. Evidence bắt buộc

Mỗi hoạt động quan trọng cần lưu ticket/change ID, người thực hiện, thời gian, input, script/log, kết quả trước/sau, dashboard/export và xác nhận hoàn tất. Với backup, restore, failover, CHECKDB, permission change và force plan, evidence phải đủ để một DBA khác đọc lại và hiểu quyết định vận hành.

## 13. Điều kiện exception

Exception bắt buộc khi không đáp ứng checklist như không chạy CHECKDB, không có log backup theo RPO, không bật monitoring, không kiểm tra restore, không dùng listener chuẩn hoặc cấp quyền vượt role chuẩn. Mỗi exception cần owner, lý do, rủi ro, ngày hết hạn và approval.

## 14. Tiêu chí nghiệm thu

Phụ lục được xem là áp dụng đúng khi SQL Server estate có baseline rõ ràng, backup/restore được kiểm chứng, HA/DR có drill, monitoring có alert, permission có audit, capacity có forecast, performance change có evidence và mọi exception được quản lý.

