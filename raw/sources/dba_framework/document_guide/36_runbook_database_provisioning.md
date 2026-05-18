---
doc_id: RBK-036
title: "Runbook Database Provisioning"
doc_type: runbook
implementation_order: 5
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
  - DBA-STD-013 Database Naming Convention Standard
  - DBA-POL-010 Backup and Restore Policy
  - DBA-STD-018 Database Monitoring Standard
  - DBA-RBK-037 Runbook User Provisioning
  - DBA-RBK-042 Runbook Database Health Check
  - DBA-TMP-048 Template Operation Evidence
---

# RBK-036 - Runbook Database Provisioning

## 1. Mục đích

Tự động hóa hoặc bán tự động hóa việc tạo database mới theo chuẩn doanh nghiệp, bảo đảm database được tạo đúng naming convention, owner, cấu hình baseline, backup policy, monitoring, alert, security classification và evidence bàn giao.

## 2. Mô tả tóm tắt

Script hoặc playbook tạo database theo yêu cầu đã được phê duyệt, kiểm tra trùng tên, cấu hình tham số cơ bản, gán owner, tạo role hoặc service account cần thiết, áp dụng backup/retention/encryption, đăng ký monitoring, cập nhật inventory/CMDB và xuất evidence vào ticket.

## 3. Phạm vi áp dụng

Runbook áp dụng cho production, staging, UAT, development và các môi trường non-production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung này là baseline chung; khác biệt riêng của SQL Server, Azure SQL Database, Azure SQL Managed Instance, PostgreSQL, MySQL/MariaDB, Oracle hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.

## 4. Nguyên tắc thiết kế runbook

1. Runbook phải idempotent ở mức có thể, hoặc phải kiểm tra trạng thái trước khi thay đổi.
2. Không hard code credential, secret hoặc thông tin nhạy cảm trong script.
3. Mọi input phải được validate trước khi thực thi.
4. Script phải có chế độ dry run nếu thao tác có rủi ro cao.
5. Script phải xuất output có cấu trúc để lưu evidence và tích hợp ITSM.
6. Thao tác production phải dùng service account được phê duyệt.
7. Database production không được bàn giao nếu chưa có backup policy, monitoring, owner và inventory record.
8. Database tạm thời phải có expiration date hoặc cleanup owner.

## 5. Phân loại yêu cầu provisioning

| Loại yêu cầu | Mô tả | Kiểm soát bắt buộc |
|---|---|---|
| Production database | Database phục vụ dịch vụ production | RFC, owner, RPO/RTO, backup, monitoring, security class |
| Staging/UAT database | Database phục vụ kiểm thử gần production | Ticket, owner, retention, masking nếu dùng dữ liệu thật |
| Development database | Database phục vụ phát triển | Ticket, owner, quota, cleanup date nếu tạm thời |
| Temporary database | Database tạo ngắn hạn để test, restore hoặc investigation | Expiration date, owner, cleanup plan |
| Platform-managed database | Database tạo qua portal/API nền tảng | Tag, policy, cost center, monitoring target |

## 6. Input bắt buộc

| Input | Mô tả |
|---|---|
| ticket_id | Mã yêu cầu tạo database |
| environment | Production, staging, UAT, development hoặc DR |
| dbms_type | SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL, Z DB |
| database_name | Tên database theo naming convention |
| owner | Service owner hoặc application owner |
| requester | Người yêu cầu |
| approver | Người phê duyệt |
| sizing | CPU, memory, storage, expected growth nếu có |
| rpo_rto | Yêu cầu backup và restore |
| security_class | Phân loại dữ liệu |
| retention | Yêu cầu retention |
| cost_center | Mã chi phí hoặc service code nếu áp dụng |
| expiration_date | Ngày hết hạn nếu database tạm thời |
| monitoring_profile | Bộ metric/alert cần áp dụng |

## 7. Output bắt buộc

1. Database được tạo đúng naming convention.
2. Owner và role cơ bản được cấu hình.
3. Backup policy hoặc PITR/LTR retention được gán.
4. Monitoring và alert được bật.
5. Database được thêm vào inventory hoặc CMDB.
6. Evidence được lưu vào ticket.
7. Tag hoặc metadata quản trị được gán nếu nền tảng hỗ trợ.
8. Expiration hoặc review date được ghi nhận nếu database tạm thời.

## 8. Luồng tự động hóa

1. Validate ticket, requester, approver và phạm vi yêu cầu.
2. Kiểm tra tên database có hợp lệ và chưa tồn tại.
3. Kiểm tra sizing, quota, target instance/server/pool và capacity forecast.
4. Tạo database hoặc gọi API provisioning của nền tảng.
5. Cấu hình parameter cơ bản theo standard.
6. Tạo role và service account nếu được yêu cầu.
7. Gán backup policy, retention, encryption nếu có.
8. Đăng ký database vào monitoring.
9. Tạo dashboard hoặc tag monitoring nếu cần.
10. Cập nhật inventory/CMDB.
11. Chạy post-check.
12. Xuất evidence và cập nhật ticket.
13. Thông báo requester hoặc service owner về kết quả bàn giao.

## 9. Post-check

| Kiểm tra | Kết quả mong muốn |
|---|---|
| Database online | Database sẵn sàng kết nối |
| Naming | Đúng convention |
| Owner | Đúng service owner |
| Permission | Role tối thiểu được cấp |
| Backup | Có policy, job backup, PITR hoặc retention phù hợp |
| Monitoring | Có metric và alert cơ bản |
| Inventory | Đã cập nhật CMDB hoặc inventory |
| Connectivity | Service account hoặc requester test kết nối thành công |
| Security baseline | Encryption, audit, firewall/private endpoint hoặc policy liên quan đúng chuẩn |
| Cost/tag | Tag owner, environment, service hoặc cost center được gán nếu nền tảng hỗ trợ |

## 10. Gợi ý triển khai theo nền tảng

### 10.1. SQL Server

1. Kiểm tra instance, drive, collation, compatibility level và recovery model mặc định.
2. Tạo database với file data/log, initial size và autogrowth theo standard.
3. Bật CHECKSUM page verification, Query Store nếu workload phù hợp và owner chuẩn.
4. Gán database vào backup job hoặc policy tương ứng.
5. Tạo role tối thiểu, ánh xạ user/service account theo yêu cầu đã duyệt.
6. Đăng ký SQL Agent job, monitoring, alert và capacity baseline.

### 10.2. Azure SQL Database

1. Xác định logical server, elastic pool hoặc single database.
2. Chọn compute tier, storage max size, backup retention, zone redundancy nếu cần.
3. Kiểm tra firewall/private endpoint, Microsoft Entra ID và TDE.
4. Bật diagnostic settings, Azure Monitor alert và Query Store.
5. Gán tag owner, environment, service, cost center và data classification nếu dùng.

### 10.3. Azure SQL Managed Instance

1. Xác định managed instance, subnet, storage, vCore và service tier.
2. Kiểm tra SQL Agent, backup retention, VNet/private connectivity và security baseline.
3. Tạo database hoặc restore database theo nguồn đã duyệt.
4. Bật monitoring, alert, Query Store và inventory mapping.

## 11. Điều kiện dừng

Dừng provisioning nếu gặp một trong các điều kiện sau:

1. Không có approval hợp lệ.
2. Tên database không đúng chuẩn hoặc đã tồn tại.
3. Không xác định được owner hoặc service liên quan.
4. Sizing hoặc storage không đủ so với yêu cầu tối thiểu.
5. Không xác định được backup/retention policy cho môi trường production.
6. Security class yêu cầu kiểm soát nhưng input chưa đủ.
7. Không thể đăng ký monitoring hoặc inventory cho database production.

## 12. Rollback

1. Nếu tạo database thất bại trước khi bàn giao, xóa tài nguyên đã tạo nếu an toàn.
2. Nếu đã cấp quyền, revoke quyền trước khi xóa.
3. Nếu đã đăng ký monitoring, remove khỏi monitoring để tránh alert giả.
4. Nếu đã cập nhật CMDB, ghi trạng thái failed hoặc cancelled.
5. Nếu đã tạo cost/tag hoặc dashboard, đánh dấu cancelled hoặc remove theo quy trình.
6. Ghi rõ phần nào đã rollback, phần nào còn tồn tại và owner xử lý tiếp.

## 13. Tài liệu liên quan

1. STD-013 Naming Convention Standard.
2. POL-010 Backup and Restore Policy.
3. STD-018 Monitoring Standard.
4. RBK-037 User Provisioning.
5. RBK-042 Database Health Check.

## 14. Yêu cầu evidence

Mọi hoạt động phát sinh từ tài liệu này cần để lại evidence tối thiểu sau:

1. Ticket ID hoặc change ID.
2. Người yêu cầu, người thực hiện, người phê duyệt nếu có.
3. Thời gian bắt đầu và kết thúc.
4. Input đã sử dụng.
5. Output hoặc log thao tác.
6. Kết quả kiểm tra sau thực hiện.
7. Đường dẫn dashboard, report, script output hoặc artifact liên quan.
8. Post-check result và xác nhận bàn giao.
9. Inventory/CMDB record hoặc tag evidence.

## 15. Tiêu chí nghiệm thu

1. Nội dung được áp dụng nhất quán cho các database thuộc phạm vi quản lý.
2. Có evidence chứng minh việc thực hiện đúng quy trình.
3. Có owner chịu trách nhiệm duy trì tài liệu.
4. Có chu kỳ review định kỳ hoặc review sau sự cố lớn.
5. Có liên kết đến policy, SOP, runbook hoặc template liên quan.
6. Database được tạo đúng chuẩn và có thể được health check tự động.
7. Backup, monitoring, inventory và quyền truy cập tối thiểu đã hoàn tất.

## 16. Chỉ số liên quan

| KPI | Công thức tham khảo |
|---|---|
| Provisioning success rate | Successful provisioning / Total provisioning requests |
| Provisioning lead time | Handover time - Request approved time |
| Post-provisioning defect count | Defects found after handover |
| Inventory compliance rate | Databases registered / Databases provisioned |
| Backup policy compliance rate | Databases with backup policy / Databases provisioned |

## 17. Quản trị thay đổi tài liệu

Tài liệu phải được review ít nhất mỗi 6 tháng hoặc sau các sự kiện sau:

1. Có sự cố P1 hoặc P2 liên quan.
2. Có thay đổi lớn về kiến trúc database.
3. Có thay đổi về yêu cầu bảo mật, audit hoặc compliance.
4. Có thay đổi công cụ vận hành hoặc nền tảng cloud.
5. Có phát hiện sai lệch giữa tài liệu và thực tế vận hành.
