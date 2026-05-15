---
doc_id: APP-056
title: "SQL Server Operations Appendix"
doc_type: appendix
implementation_order: 2
status: draft
owner: DBA Team
reviewer: DBA Lead
approver: Service Owner
version: 0.1
created_date: 2026-05-15
last_updated: 2026-05-15
language: vi
framework: DBA Operations Framework
---

# APP-056 - SQL Server Operations Appendix

## 1. Mục đích

Đặc tả riêng cho SQL Server.

## 2. Mô tả tóm tắt

Tổng hợp mapping policy chung sang SQL Server, gồm Always On, SQL Agent, backup, restore, CHECKDB, index maintenance, Query Store và monitoring.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc sử dụng phụ lục DBMS

1. Policy và standard chung vẫn là nguồn kiểm soát chính.
2. Phụ lục này dùng để chuyển yêu cầu chung thành thao tác và kiểm tra phù hợp DBMS cụ thể.
3. Khi có khác biệt giữa DBMS và policy chung, phải ghi rõ exception, owner và approval.
4. Các lệnh cụ thể phải được kiểm chứng trong môi trường test trước khi đưa vào runbook production.


## 4. Phạm vi SQL Server

Phụ lục này áp dụng cho SQL Server on VM, SQL Server on premise và các triển khai SQL Server có mô hình Always On, Failover Cluster Instance, SQL Agent, maintenance job hoặc backup native.

## 5. Mapping vận hành chính

| Chủ đề | Cơ chế SQL Server | Tài liệu chung liên quan |
|---|---|---|
| Backup | Full, differential, transaction log backup | Backup and Restore Policy |
| Restore | Restore database, restore log, point in time restore | SOP Restore Operation |
| HA | Always On Availability Groups, FCI | HA and DR Policy |
| Failover | AG failover, listener, cluster role | SOP HA Failover and Switchover |
| Integrity | DBCC CHECKDB | SOP Data Corruption Handling |
| Performance | Query Store, execution plan, wait stats | SOP Query Tuning Review |
| Job | SQL Agent | SOP Daily Health Check |
| Index | Rebuild, reorganize, statistics update | Database Indexing Standard |
| Monitoring | DMVs, error log, Extended Events | Database Monitoring Standard |

## 6. Backup và restore SQL Server

1. Full backup là baseline phục hồi chính.
2. Differential backup có thể dùng để giảm thời gian restore nếu phù hợp.
3. Transaction log backup bắt buộc với database cần PITR trong full recovery model.
4. Backup chain phải được kiểm tra trong Runbook Backup Verification.
5. Restore validation cần kiểm tra database online, consistency, object count và ứng dụng nếu có.

## 7. Always On và failover

1. Theo dõi synchronization state, synchronization health và redo queue.
2. Listener phải được kiểm tra sau failover.
3. Application connection string cần dùng listener hoặc endpoint chuẩn.
4. Manual failover phải có approval nếu là production.
5. Failover drill cần đo RTO thực tế và ghi evidence.

## 8. CHECKDB và corruption

1. DBCC CHECKDB cần được chạy định kỳ theo policy phù hợp tải hệ thống.
2. Kết quả CHECKDB phải được lưu evidence.
3. Không dùng repair allow data loss nếu chưa có approval rõ ràng.
4. Với lỗi nghiêm trọng, ưu tiên restore từ backup hợp lệ hoặc phục hồi theo hướng dẫn DBA Lead.

## 9. Query Store và performance

1. Query Store nên bật cho database cần theo dõi plan regression nếu workload phù hợp.
2. Cần theo dõi top queries theo duration, CPU, logical reads và execution count.
3. Khi force plan, phải ghi lý do, thời hạn review và rollback plan.
4. Index recommendation không được áp dụng tự động nếu chưa review workload.

## 10. SQL Server checklist vận hành

| Hạng mục | Kiểm tra |
|---|---|
| Backup | Full, diff, log backup đúng policy |
| Restore | Có restore drill và validation |
| HA | AG health, listener, replica sync |
| Integrity | CHECKDB gần nhất không lỗi |
| Job | SQL Agent job failed count |
| Security | Login, role, sysadmin membership |
| Performance | Query Store, wait stats, blocking |
| Capacity | Data, log, tempdb, backup storage |

## 9. Tài liệu liên quan

1. APP-060 Azure SQL Operations Appendix.
2. SOP-023 Backup Operation.
3. SOP-028 HA Failover and Switchover.

## 10. Yêu cầu evidence

Mọi hoạt động phát sinh từ tài liệu này cần để lại evidence tối thiểu sau:

1. Ticket ID hoặc change ID.
2. Người yêu cầu, người thực hiện, người phê duyệt nếu có.
3. Thời gian bắt đầu và kết thúc.
4. Input đã sử dụng.
5. Output hoặc log thao tác.
6. Kết quả kiểm tra sau thực hiện.
7. Đường dẫn dashboard, report, script output hoặc artifact liên quan.

## 11. Tiêu chí nghiệm thu

1. Nội dung được áp dụng nhất quán cho các database thuộc phạm vi quản lý.
2. Có evidence chứng minh việc thực hiện đúng quy trình.
3. Có owner chịu trách nhiệm duy trì tài liệu.
4. Có chu kỳ review định kỳ hoặc review sau sự cố lớn.
5. Có liên kết đến policy, SOP, runbook hoặc template liên quan.

## 12. Quản trị thay đổi tài liệu

Tài liệu phải được review ít nhất mỗi 6 tháng hoặc sau các sự kiện sau:

1. Có sự cố P1 hoặc P2 liên quan.
2. Có thay đổi lớn về kiến trúc database.
3. Có thay đổi về yêu cầu bảo mật, audit hoặc compliance.
4. Có thay đổi công cụ vận hành hoặc nền tảng cloud.
5. Có phát hiện sai lệch giữa tài liệu và thực tế vận hành.
