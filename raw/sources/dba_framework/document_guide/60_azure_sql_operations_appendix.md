---
doc_id: APP-060
title: "Azure SQL Operations Appendix"
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

# APP-060 - Azure SQL Operations Appendix

## 1. Mục đích

Đặc tả riêng cho Azure SQL.

## 2. Mô tả tóm tắt

Tổng hợp mapping policy chung sang Azure SQL Database và Managed Instance, gồm backup built in, PITR, long term retention, Query Store, elastic pool, SQL Agent alternative và Azure Monitor.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc sử dụng phụ lục DBMS

1. Policy và standard chung vẫn là nguồn kiểm soát chính.
2. Phụ lục này dùng để chuyển yêu cầu chung thành thao tác và kiểm tra phù hợp DBMS cụ thể.
3. Khi có khác biệt giữa DBMS và policy chung, phải ghi rõ exception, owner và approval.
4. Các lệnh cụ thể phải được kiểm chứng trong môi trường test trước khi đưa vào runbook production.


## 4. Phạm vi Azure SQL

Phụ lục này áp dụng cho Azure SQL Database và Azure SQL Managed Instance. Với SQL Server cài trên Azure VM, sử dụng SQL Server Operations Appendix kết hợp với tiêu chuẩn cloud và hạ tầng Azure liên quan.

## 5. Mapping dịch vụ Azure SQL

| Chủ đề | Azure SQL Database | Azure SQL Managed Instance |
|---|---|---|
| Backup | Built in automated backup, PITR, LTR | Built in automated backup, PITR, LTR |
| HA | Built in HA, zone redundancy nếu cấu hình | Built in HA, zone redundancy nếu cấu hình |
| DR | Geo restore, active geo replication, failover group | Failover group, geo restore |
| Job | Elastic Jobs hoặc external scheduler | SQL Agent hỗ trợ trên Managed Instance |
| Monitoring | Azure Monitor, metrics, diagnostic settings | Azure Monitor, metrics, diagnostic settings |
| Performance | Query Store, automatic tuning nếu bật | Query Store, gần SQL Server hơn |
| Security | Microsoft Entra ID, firewall, private endpoint, TDE | Microsoft Entra ID, VNet integration, TDE |

## 6. Backup và PITR

1. Azure SQL có backup tự động built in, nhưng DBA vẫn phải kiểm tra policy, retention và khả năng restore.
2. PITR phải được kiểm chứng bằng restore drill định kỳ.
3. Long Term Retention cần cấu hình nếu có yêu cầu lưu trữ dài hạn.
4. Geo restore hoặc failover group phải được kiểm tra theo yêu cầu DR.
5. Evidence restore phải ghi rõ source, target, restore point và thời gian hoàn tất.

## 7. Failover và DR

1. Failover group cần được thiết kế theo application connection strategy.
2. Ứng dụng nên dùng listener endpoint của failover group nếu yêu cầu DR tự động hoặc bán tự động.
3. Cần đo RTO và RPO thực tế trong failover drill.
4. Sau failover phải kiểm tra connectivity, role primary, replication state và ứng dụng.
5. Planned failover phải có RFC, còn emergency failover phải có emergency change và post review.

## 8. Monitoring Azure SQL

1. Bật diagnostic settings để gửi log và metric tới nơi giám sát được phê duyệt.
2. Theo dõi DTU hoặc vCore utilization, CPU, data IO, log IO, storage, deadlock và connection failure.
3. Query Store là nguồn quan trọng để điều tra query regression.
4. Alert phải map với SOP tương ứng như backup, capacity, performance, failover.

## 9. SQL Agent alternative

| Nhu cầu | Azure SQL Database | Azure SQL Managed Instance |
|---|---|---|
| Scheduled job | Elastic Jobs, Azure Automation, Logic Apps, Function, Data Factory | SQL Agent hoặc Azure Automation |
| Maintenance | External automation hoặc built in feature | SQL Agent job nếu phù hợp |
| Monitoring task | Azure Monitor, Automation, Function | Azure Monitor, SQL Agent, Automation |

## 10. Azure SQL checklist vận hành

| Hạng mục | Kiểm tra |
|---|---|
| Backup | PITR retention và LTR nếu cần |
| Restore | Restore drill sang target test |
| HA | Zone redundancy hoặc built in HA theo tier |
| DR | Failover group hoặc geo restore readiness |
| Security | Firewall, private endpoint, Entra ID, TDE |
| Performance | Query Store, wait stats, automatic tuning setting |
| Capacity | Storage, vCore, DTU, elastic pool usage |
| Monitoring | Azure Monitor, diagnostic settings, alert rule |

## 9. Tài liệu liên quan

1. APP-056 SQL Server Operations Appendix.
2. POL-010 Backup and Restore Policy.
3. POL-012 HA and DR Policy.

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
