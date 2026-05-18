---
doc_id: APP-056
title: "SQL Server Operations Appendix"
doc_type: appendix
implementation_order: 2
status: ready_for_review
owner: DBA Team
reviewer: DBA Lead
approver: Service Owner
version: 1.0
created_date: 2026-05-15
last_updated: 2026-05-18
language: vi
framework: DBA Operations Framework
related_documents:
  - APP-060 Azure SQL Operations Appendix
  - DBA-POL-005 Backup and Restore Policy
  - DBA-POL-007 Database HA and DR Policy
  - DBA-SOP-023 SOP Backup Operation
  - DBA-SOP-024 SOP Restore Operation
  - DBA-SOP-028 SOP HA Failover and Switchover
  - DBA-RBK-038 Runbook Backup Verification
  - DBA-RBK-039 Runbook Restore Validation
  - DBA-RBK-041 Runbook Failover Execution
  - DBA-RBK-042 Runbook Database Health Check
---

# APP-056 - SQL Server Operations Appendix

## 1. Mục đích

Phụ lục này đặc tả cách áp dụng DBA Operations Framework cho SQL Server. Nội dung dùng để chuyển policy, standard, SOP và runbook chung thành checklist, thao tác kiểm chứng, evidence và exception phù hợp với SQL Server on-premises, SQL Server on VM, Always On Availability Groups, Failover Cluster Instance, SQL Agent và backup native.

## 2. Mô tả tóm tắt

Tài liệu tổng hợp mapping policy chung sang SQL Server, gồm cấu hình baseline, backup/restore, Always On, failover, CHECKDB, SQL Agent, index/statistics maintenance, Query Store, Extended Events, DMVs, security auditing, monitoring, capacity và evidence vận hành.

## 3. Phạm vi áp dụng

Áp dụng cho SQL Server on-premises, SQL Server on VM và các triển khai SQL Server có mô hình Always On, Failover Cluster Instance, SQL Agent, maintenance job hoặc backup native. Với SQL Server chạy trên Azure VM, dùng phụ lục này kết hợp tiêu chuẩn cloud và hạ tầng Azure liên quan. Với Azure SQL Database hoặc Azure SQL Managed Instance, dùng APP-060.

## 4. Nguyên tắc sử dụng phụ lục DBMS

1. Policy và standard chung vẫn là nguồn kiểm soát chính.
2. Phụ lục này dùng để chuyển yêu cầu chung thành thao tác và kiểm tra phù hợp SQL Server.
3. Khi có khác biệt giữa SQL Server và policy chung, phải ghi rõ exception, owner và approval.
4. Các lệnh cụ thể phải được kiểm chứng trong môi trường test trước khi đưa vào runbook production.
5. Không áp dụng script tuning, force plan, rebuild index hàng loạt, shrink file hoặc repair option nếu chưa có review và approval.
6. Mọi thao tác production phải đi qua ticket, evidence và rollback/fallback khi có rủi ro.

## 5. Mapping vận hành chính

| Chủ đề | Cơ chế SQL Server | Tài liệu chung liên quan | Evidence tối thiểu |
|---|---|---|---|
| Backup | Full, differential, transaction log backup, compression, encryption | Backup and Restore Policy, RBK-038 | Backup report, backup age, chain status |
| Restore | Restore database/log, PITR, restore with move | SOP Restore Operation, RBK-039 | Restore log, recovery point, validation report |
| HA | Always On Availability Groups, FCI, log shipping | HA and DR Policy, RBK-040 | Replica health, listener, lag, topology |
| Failover | AG failover, listener, cluster role | SOP HA Failover and Switchover, RBK-041 | Timeline, RTO/RPO, post-check |
| Integrity | DBCC CHECKDB, suspect pages | SOP Data Corruption Handling | CHECKDB output, action item |
| Performance | Query Store, execution plan, wait stats, DMVs | SOP Query Tuning Review | Query, plan, waits, before/after |
| Job | SQL Agent, Database Mail, operators | SOP Daily Health Check | Failed job count, notification evidence |
| Index | Rebuild, reorganize, statistics update | Database Indexing Standard | Change script, metrics before/after |
| Security | Login, role, sysadmin, audit, encryption | Access Control Policy | Permission before/after, audit output |
| Monitoring | DMVs, error log, Extended Events | Database Monitoring Standard | Dashboard/export, alert state |
| Capacity | Data/log/tempdb/backup storage | Capacity Management Standard | Growth trend, forecast, owner |

## 6. Baseline cấu hình SQL Server

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
| MaxDOP/CTFP | Theo baseline hạ tầng và workload, có evidence khi thay đổi |

## 7. Backup và restore SQL Server

1. Full backup là baseline phục hồi chính.
2. Differential backup có thể dùng để giảm thời gian restore nếu phù hợp.
3. Transaction log backup bắt buộc với database cần PITR trong FULL recovery model.
4. Backup chain phải được kiểm tra trong RBK-038 Runbook Backup Verification.
5. Restore validation cần kiểm tra database online, recovery point, consistency, object count, row count hoặc business sample query.
6. Restore drill phải chạy định kỳ, không chỉ kiểm tra file backup tồn tại.
7. Backup encryption, compression và retention phải theo policy đã phê duyệt.

## 8. Always On, FCI và failover

1. Theo dõi replica role, synchronization state, synchronization health, log send queue, redo queue và failover readiness.
2. Listener, DNS, firewall và application connection string phải được kiểm tra trước và sau failover.
3. Manual failover phải có approval nếu là production.
4. Forced failover chỉ dùng khi có emergency approval và chấp nhận rủi ro mất dữ liệu.
5. Failover drill cần ghi timeline, RTO/RPO thực tế, application recovery time và stakeholder confirmation.
6. Khi thêm replica, phải kiểm tra seed data, endpoint, credential, network, lag, read-only routing nếu dùng và backup chain impact.

## 9. CHECKDB và corruption

1. DBCC CHECKDB cần được chạy định kỳ theo policy phù hợp tải hệ thống.
2. Kết quả CHECKDB phải được lưu evidence.
3. Nếu không thể chạy CHECKDB đầy đủ trên production, phải có phương án thay thế được phê duyệt như restore sang môi trường khác để kiểm tra.
4. Không dùng REPAIR_ALLOW_DATA_LOSS nếu chưa có approval rõ ràng từ DBA Lead và Service Owner.
5. Corruption finding phải được xử lý như incident, có evidence, impact assessment và recovery plan.

## 10. Query Store, performance và index

1. Query Store nên bật cho database cần theo dõi plan regression nếu workload phù hợp.
2. Theo dõi top queries theo duration, CPU, logical reads, execution count và wait category.
3. Khi force plan, phải ghi lý do, thời hạn review, tiêu chí rollback và evidence trước/sau.
4. Index recommendation không được áp dụng tự động nếu chưa review workload.
5. Không rebuild index hàng loạt nếu không có tiêu chí fragmentation, page count, maintenance window và rollback.
6. Cập nhật statistics cần cân nhắc sample, fullscan, workload và plan stability.

## 11. SQL Agent và automation

1. SQL Agent job phải có owner chuẩn, lịch chạy, retry policy và notification.
2. Job production không nên do tài khoản cá nhân sở hữu.
3. Failed job phải được đưa vào health check và alert.
4. Maintenance job cần evidence, output retention và review sau thay đổi.
5. Các job backup, CHECKDB, index maintenance và data purge phải có runbook hoặc SOP liên quan.

## 12. Security và audit

1. Kiểm tra sysadmin membership, fixed server roles, database owner, orphan users và login không còn hợp lệ.
2. Human user trên production cần approval, role chuẩn và review định kỳ hoặc expiration.
3. Service account và application account phải có owner, purpose, rotation hoặc review process.
4. Audit evidence phải thể hiện quyền trước/sau thay đổi.
5. Encryption at rest và in transit phải được kiểm tra theo policy.
6. Failed login spike hoặc privileged role drift phải tạo alert hoặc ticket.

## 13. Monitoring và health check

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

## 14. Capacity và storage

1. Theo dõi data file, log file, tempdb, backup storage và autogrowth event.
2. Dự báo days_to_threshold cho từng drive hoặc storage pool.
3. Không shrink file định kỳ như biện pháp capacity management.
4. Log growth bất thường phải điều tra transaction dài, backup log, replication hoặc batch job.
5. Tempdb tăng bất thường phải điều tra query spill, index operation, version store hoặc ETL.

## 15. Điều kiện exception

Exception bắt buộc khi không đáp ứng checklist như không chạy CHECKDB, không có log backup theo RPO, không bật monitoring, không kiểm tra restore, không dùng listener chuẩn, cấp quyền vượt role chuẩn hoặc không có evidence cho thay đổi production. Mỗi exception cần owner, lý do, rủi ro, ngày hết hạn và approval.

## 16. Yêu cầu evidence

Mọi hoạt động phát sinh từ tài liệu này cần để lại evidence tối thiểu sau:

1. Ticket ID hoặc change ID.
2. Người yêu cầu, người thực hiện, người phê duyệt nếu có.
3. Thời gian bắt đầu và kết thúc.
4. Input đã sử dụng.
5. Output hoặc log thao tác.
6. Kết quả kiểm tra sau thực hiện.
7. Đường dẫn dashboard, report, script output hoặc artifact liên quan.
8. Với backup, restore, failover, CHECKDB, permission change và force plan, evidence phải đủ để DBA khác đọc lại và hiểu quyết định vận hành.

## 17. Tiêu chí nghiệm thu

1. SQL Server estate có baseline rõ ràng.
2. Backup/restore được kiểm chứng.
3. HA/DR có drill và evidence.
4. Monitoring có alert và owner.
5. Permission có audit.
6. Capacity có forecast.
7. Performance change có before/after evidence.
8. Mọi exception được quản lý.

## 18. Quản trị thay đổi tài liệu

Tài liệu phải được review ít nhất mỗi 6 tháng hoặc sau các sự kiện sau:

1. Có sự cố P1 hoặc P2 liên quan.
2. Có thay đổi lớn về kiến trúc database.
3. Có thay đổi về yêu cầu bảo mật, audit hoặc compliance.
4. Có thay đổi công cụ vận hành hoặc nền tảng cloud.
5. Có phát hiện sai lệch giữa tài liệu và thực tế vận hành.
