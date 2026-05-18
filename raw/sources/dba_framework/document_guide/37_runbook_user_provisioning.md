---
doc_id: RBK-037
title: "Runbook User Provisioning"
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
  - DBA-POL-008 Database Access Control Policy
  - DBA-SOP-022 SOP User and Permission Management
  - DBA-TMP-048 Template Operation Evidence
---

# RBK-037 - Runbook User Provisioning

## 1. Mục đích

Tự động hóa việc tạo user, cấp quyền, kiểm tra quyền và thu hồi quyền theo nguyên tắc least privilege, approval bắt buộc, audit trước/sau thay đổi và evidence đầy đủ.

## 2. Mô tả tóm tắt

Script hoặc playbook tạo user hoặc ánh xạ account hiện có, gán role chuẩn, kiểm tra quyền hiện tại để tránh cấp trùng/cấp vượt, chạy verification query, xuất evidence và liên kết toàn bộ thay đổi với ticket yêu cầu.

## 3. Phạm vi áp dụng

Runbook áp dụng cho production, staging, UAT, development và các môi trường non-production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung này là baseline chung; khác biệt riêng của SQL Server, Azure SQL, PostgreSQL, MySQL/MariaDB, Oracle hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.

## 4. Nguyên tắc thiết kế runbook

1. Runbook phải idempotent ở mức có thể, hoặc phải kiểm tra trạng thái trước khi thay đổi.
2. Không hard code credential, secret hoặc thông tin nhạy cảm trong script.
3. Mọi input phải được validate trước khi thực thi.
4. Script phải có chế độ dry run nếu thao tác có rủi ro cao.
5. Script phải xuất output có cấu trúc để lưu evidence và tích hợp ITSM.
6. Thao tác production phải dùng service account được phê duyệt.

## 5. Phân loại account

| Loại account | Mục đích | Kiểm soát bắt buộc |
|---|---|---|
| Human user | Người dùng truy cập trực tiếp | Approval, role chuẩn, review định kỳ hoặc expiration |
| Service account | Automation, job, integration | Owner, purpose, secret rotation, dependency check |
| Application account | Ứng dụng kết nối database | Service owner, connection path, least privilege |
| Break-glass account | Truy cập khẩn cấp | Emergency approval, thời hạn ngắn, post-review |
| Read-only account | Báo cáo, audit, kiểm tra | Scope rõ, không có quyền ghi |

## 6. Input bắt buộc

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
| access_scope | Database, schema, object hoặc environment được phép truy cập |
| business_justification | Lý do nghiệp vụ hoặc vận hành |
| expiration_date | Ngày hết hạn nếu quyền tạm thời |

## 7. Nguyên tắc tự động hóa

1. Script không cấp quyền nếu thiếu approval.
2. Không cấp quyền cao hơn role đã chuẩn hóa nếu không có exception.
3. Quyền production cho human user phải có thời hạn hoặc quy trình review định kỳ.
4. Mọi thay đổi quyền phải được audit.
5. Output phải thể hiện quyền trước và sau khi thay đổi.
6. Human user trên production phải tránh quyền trực tiếp dài hạn nếu có thể dùng quy trình privileged access.
7. Service account phải có owner và mục đích sử dụng rõ ràng.
8. Không cấp quyền sysadmin, db_owner hoặc tương đương nếu không có exception được phê duyệt.

## 8. Luồng thực thi

1. Validate ticket và approval.
2. Kiểm tra account có tồn tại không.
3. Kiểm tra role yêu cầu có nằm trong danh mục chuẩn không.
4. Kiểm tra quyền hiện tại để tránh cấp trùng hoặc cấp vượt.
5. Tạo user nếu được phép.
6. Gán role hoặc permission.
7. Chạy verification query.
8. Ghi audit evidence.
9. Cập nhật ticket và thời hạn review nếu có.
10. Thông báo requester hoặc service owner sau khi hoàn tất.
11. Nếu quyền có thời hạn, tạo reminder hoặc task thu hồi.

## 9. Verification output

| Trường | Mô tả |
|---|---|
| account_name | User hoặc service account |
| database_name | Database được cấp quyền |
| granted_role | Role đã gán |
| previous_permission | Quyền trước thay đổi |
| current_permission | Quyền sau thay đổi |
| expiration_date | Ngày hết hạn nếu có |
| evidence_path | Đường dẫn evidence |
| verification_result | PASS, WARN, FAIL |
| reviewer | Người kiểm tra hoặc phê duyệt kết quả |

## 10. Gợi ý thao tác theo DBMS

### 10.1. SQL Server

1. Kiểm tra login ở server level và user ở database level.
2. Tránh orphaned user bằng cách kiểm tra SID mapping khi cần.
3. Gán role database chuẩn thay vì cấp quyền rời rạc nếu có thể.
4. Kiểm tra fixed server role, db_owner, sysadmin và explicit permission trước/sau thay đổi.
5. Lưu output từ catalog view hoặc script audit quyền.

### 10.2. Azure SQL Database

1. Ưu tiên Microsoft Entra ID group nếu phù hợp với mô hình truy cập.
2. Kiểm tra firewall/private endpoint trước khi kết luận user không truy cập được.
3. Gán contained database user hoặc role theo chuẩn.
4. Lưu evidence từ Azure Activity Log, audit log hoặc query output.

### 10.3. Azure SQL Managed Instance

1. Kiểm tra login, user, role và SQL Agent job dependency nếu là service account.
2. Áp dụng cách quản trị gần SQL Server nhưng vẫn ghi nhận network và identity theo Azure.

## 11. Điều kiện dừng

1. Thiếu approval hoặc approver không hợp lệ.
2. Role yêu cầu không nằm trong danh mục chuẩn và không có exception.
3. Không xác định được owner của account.
4. Account yêu cầu quyền production dài hạn nhưng không có justification.
5. Có dấu hiệu cấp quyền vượt nhu cầu hoặc vi phạm segregation of duties.
6. Không thể lưu audit evidence.

## 12. Thu hồi quyền

1. Xác định quyền cần thu hồi từ ticket hoặc access review.
2. Kiểm tra dependency với application account.
3. Revoke role hoặc permission.
4. Kiểm tra user không còn quyền không mong muốn.
5. Lưu evidence và cập nhật ticket.
6. Nếu account không còn owner hoặc không còn sử dụng, disable hoặc drop theo policy đã phê duyệt.

## 13. Rollback

| Tình huống | Hướng xử lý |
|---|---|
| Cấp sai role | Revoke role vừa cấp, chạy lại verification |
| Tạo nhầm user | Revoke quyền, drop user nếu an toàn và chưa có dependency |
| Cấp vượt quyền | Revoke ngay, escalate DBA Lead và Security nếu production |
| Không chạy được verification | Không bàn giao, giữ ticket ở trạng thái pending |
| Service account bị ảnh hưởng | Khôi phục quyền trước đó nếu có evidence và approval |

## 14. Tài liệu liên quan

1. POL-008 Access Control Policy.
2. SOP-022 User and Permission Management.
3. TPL-048 Operation Evidence.

## 15. Yêu cầu evidence

Mọi hoạt động phát sinh từ tài liệu này cần để lại evidence tối thiểu sau:

1. Ticket ID hoặc change ID.
2. Người yêu cầu, người thực hiện, người phê duyệt nếu có.
3. Thời gian bắt đầu và kết thúc.
4. Input đã sử dụng.
5. Output hoặc log thao tác.
6. Kết quả kiểm tra sau thực hiện.
7. Đường dẫn dashboard, report, script output hoặc artifact liên quan.
8. Quyền trước và sau thay đổi.
9. Verification query output.
10. Expiration/review date nếu có.

## 16. Tiêu chí nghiệm thu

1. Nội dung được áp dụng nhất quán cho các database thuộc phạm vi quản lý.
2. Có evidence chứng minh việc thực hiện đúng quy trình.
3. Có owner chịu trách nhiệm duy trì tài liệu.
4. Có chu kỳ review định kỳ hoặc review sau sự cố lớn.
5. Có liên kết đến policy, SOP, runbook hoặc template liên quan.
6. Quyền được cấp đúng role chuẩn, đúng scope, đúng thời hạn.
7. Evidence đủ để audit lại thay đổi quyền.

## 17. Chỉ số liên quan

| KPI | Công thức tham khảo |
|---|---|
| Access provisioning lead time | Grant completed time - Approval time |
| Access request rejection rate | Rejected requests / Total requests |
| Privileged access exception count | Exception grants / Total grants |
| Access review finding count | Findings during review |
| Revocation compliance rate | Revoked on time / Expired access |

## 18. Quản trị thay đổi tài liệu

Tài liệu phải được review ít nhất mỗi 6 tháng hoặc sau các sự kiện sau:

1. Có sự cố P1 hoặc P2 liên quan.
2. Có thay đổi lớn về kiến trúc database.
3. Có thay đổi về yêu cầu bảo mật, audit hoặc compliance.
4. Có thay đổi công cụ vận hành hoặc nền tảng cloud.
5. Có phát hiện sai lệch giữa tài liệu và thực tế vận hành.
