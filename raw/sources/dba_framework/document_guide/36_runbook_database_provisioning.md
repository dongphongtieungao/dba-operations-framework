---
doc_id: RBK-036
title: "Runbook Database Provisioning"
doc_type: runbook
implementation_order: 5
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

# RBK-036 - Runbook Database Provisioning

## 1. Mục đích

Tự động hóa tạo database.

## 2. Mô tả tóm tắt

Script hoặc playbook tạo database theo chuẩn naming, parameter, owner, backup policy, monitoring, alerting và ghi nhận evidence.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thiết kế runbook

1. Runbook phải idempotent ở mức có thể, hoặc phải kiểm tra trạng thái trước khi thay đổi.
2. Không hard code credential, secret hoặc thông tin nhạy cảm trong script.
3. Mọi input phải được validate trước khi thực thi.
4. Script phải có chế độ dry run nếu thao tác có rủi ro cao.
5. Script phải xuất output có cấu trúc để lưu evidence và tích hợp ITSM.
6. Thao tác production phải dùng service account được phê duyệt.


## 4. Input bắt buộc

| Input | Mô tả |
|---|---|
| ticket_id | Mã yêu cầu tạo database |
| environment | Production, staging, development |
| dbms_type | SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL, Z DB |
| database_name | Tên database theo naming convention |
| owner | Service owner hoặc application owner |
| sizing | CPU, memory, storage, expected growth nếu có |
| rpo_rto | Yêu cầu backup và restore |
| security_class | Phân loại dữ liệu |
| retention | Yêu cầu retention |

## 5. Output bắt buộc

1. Database được tạo đúng naming convention.
2. Owner và role cơ bản được cấu hình.
3. Backup policy được gán.
4. Monitoring và alert được bật.
5. Database được thêm vào inventory hoặc CMDB.
6. Evidence được lưu vào ticket.

## 6. Luồng tự động hóa

1. Validate input và approval.
2. Kiểm tra tên database có hợp lệ và chưa tồn tại.
3. Tạo database hoặc gọi API provisioning của nền tảng.
4. Cấu hình parameter cơ bản theo standard.
5. Tạo role và service account nếu được yêu cầu.
6. Gán backup policy, retention, encryption nếu có.
7. Đăng ký database vào monitoring.
8. Tạo dashboard hoặc tag monitoring nếu cần.
9. Chạy post check.
10. Xuất evidence và cập nhật ticket.

## 7. Post check

| Kiểm tra | Kết quả mong muốn |
|---|---|
| Database online | Database sẵn sàng kết nối |
| Naming | Đúng convention |
| Owner | Đúng service owner |
| Permission | Role tối thiểu được cấp |
| Backup | Có policy hoặc job backup |
| Monitoring | Có metric và alert cơ bản |
| Inventory | Đã cập nhật CMDB hoặc inventory |

## 8. Rollback

1. Nếu tạo database thất bại trước khi bàn giao, xóa tài nguyên đã tạo nếu an toàn.
2. Nếu đã cấp quyền, revoke quyền trước khi xóa.
3. Nếu đã đăng ký monitoring, remove khỏi monitoring để tránh alert giả.
4. Nếu đã cập nhật CMDB, ghi trạng thái failed hoặc cancelled.

## 9. Tài liệu liên quan

1. STD-013 Naming Convention Standard.
2. POL-010 Backup and Restore Policy.
3. STD-018 Monitoring Standard.

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
