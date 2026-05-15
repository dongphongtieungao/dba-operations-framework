---
doc_id: RBK-037
title: "Runbook User Provisioning"
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

# RBK-037 - Runbook User Provisioning

## 1. Mục đích

Tự động hóa tạo user và cấp quyền.

## 2. Mô tả tóm tắt

Script hoặc playbook tạo user, gán role, kiểm tra quyền, xuất evidence và liên kết ticket yêu cầu.

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
| ticket_id | Mã yêu cầu cấp quyền |
| requester | Người yêu cầu |
| approver | Người phê duyệt |
| environment | Môi trường yêu cầu |
| dbms_type | Loại DBMS |
| database_name | Database cần truy cập |
| account_type | Human, service account, application account |
| role_requested | Role hoặc permission yêu cầu |
| duration | Thời hạn quyền nếu là quyền tạm thời |

## 5. Nguyên tắc tự động hóa

1. Script không cấp quyền nếu thiếu approval.
2. Không cấp quyền cao hơn role đã chuẩn hóa nếu không có exception.
3. Quyền production cho human user phải có thời hạn hoặc quy trình review định kỳ.
4. Mọi thay đổi quyền phải được audit.
5. Output phải thể hiện quyền trước và sau khi thay đổi.

## 6. Luồng thực thi

1. Validate ticket và approval.
2. Kiểm tra account có tồn tại không.
3. Kiểm tra role yêu cầu có nằm trong danh mục chuẩn không.
4. Kiểm tra quyền hiện tại để tránh cấp trùng hoặc cấp vượt.
5. Tạo user nếu được phép.
6. Gán role hoặc permission.
7. Chạy verification query.
8. Ghi audit evidence.
9. Cập nhật ticket và thời hạn review nếu có.

## 7. Verification output

| Trường | Mô tả |
|---|---|
| account_name | User hoặc service account |
| database_name | Database được cấp quyền |
| granted_role | Role đã gán |
| previous_permission | Quyền trước thay đổi |
| current_permission | Quyền sau thay đổi |
| expiration_date | Ngày hết hạn nếu có |
| evidence_path | Đường dẫn evidence |

## 8. Thu hồi quyền

1. Xác định quyền cần thu hồi từ ticket hoặc access review.
2. Kiểm tra dependency với application account.
3. Revoke role hoặc permission.
4. Kiểm tra user không còn quyền không mong muốn.
5. Lưu evidence và cập nhật ticket.

## 9. Tài liệu liên quan

1. POL-008 Access Control Policy.
2. SOP-022 User and Permission Management.
3. TPL-048 Operation Evidence.

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
