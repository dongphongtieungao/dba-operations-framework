# Database Access Control Policy

## Thông tin tài liệu

1. Mã tài liệu: DBA POL 003
2. Loại tài liệu: Policy
3. Mức ưu tiên triển khai: 3
4. Owner đề xuất: DBA Team
5. Trạng thái: Draft
6. Phiên bản: 0.1
7. Phạm vi áp dụng: SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid
8. Chu kỳ review: 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật

## 1. Mục đích

Chính sách này quy định nguyên tắc tạo, cấp, sửa, thu hồi và review quyền truy cập database. Mục tiêu là đảm bảo mỗi người dùng hoặc ứng dụng chỉ có quyền cần thiết, đúng thời hạn, đúng phạm vi và có khả năng kiểm toán.

## 2. Phạm vi

Áp dụng cho user cá nhân, service account, role, group, application account và tài khoản đặc quyền trên tất cả môi trường database.

## 3. Nguyên tắc

1. Quyền truy cập phải được cấp theo role thay vì cấp trực tiếp cho từng user nếu DBMS hỗ trợ.
2. Production access phải có ticket và approval.
3. Quyền cao phải có thời hạn nếu không thuộc nhóm vận hành chính thức.
4. Không cấp quyền rộng hơn nhu cầu đã được phê duyệt.
5. Quyền không còn sử dụng phải được thu hồi.
6. Access review phải thực hiện định kỳ.

## 4. Loại tài khoản

### 4.1. Human user

Tài khoản đại diện cho cá nhân. Tài khoản này dùng cho truy cập có kiểm soát, hỗ trợ điều tra và thao tác được phê duyệt.

### 4.2. Service account

Tài khoản ứng dụng hoặc automation sử dụng. Tài khoản này phải có owner, secret management và phạm vi quyền rõ ràng.

### 4.3. Privileged account

Tài khoản có quyền quản trị cao. Tài khoản này phải được kiểm soát chặt, audit đầy đủ và review thường xuyên.

### 4.4. Break glass account

Tài khoản dùng trong trường hợp khẩn cấp khi các kênh truy cập thông thường không hoạt động. Mọi lần sử dụng phải được post review.

## 5. Quy định cấp quyền

1. Request phải nêu rõ user, database, môi trường, quyền cần cấp, lý do và thời hạn.
2. Quyền production phải được service owner hoặc security phê duyệt theo mức độ nhạy cảm.
3. DBA chỉ cấp quyền sau khi ticket đầy đủ.
4. DBA phải kiểm tra quyền sau khi cấp.
5. DBA phải lưu evidence gồm approval, lệnh hoặc công cụ đã dùng và kết quả kiểm tra.

## 6. Quy định quyền tạm thời

1. Quyền tạm thời phải có ngày hết hạn.
2. Quyền tạm thời phải được thu hồi tự động nếu có công cụ hỗ trợ.
3. Nếu chưa tự động hóa, DBA phải có lịch review và thu hồi.
4. Quyền tạm thời quá hạn phải được báo cáo.

## 7. Quy định thu hồi quyền

Quyền phải được thu hồi khi:

1. Người dùng chuyển vị trí.
2. Người dùng rời dự án.
3. Ứng dụng decommission.
4. Service account không còn dùng.
5. Quyền cấp sai phạm vi.
6. Quyền tạm thời hết hạn.
7. Security yêu cầu thu hồi.

## 8. Access review

### 8.1. Tần suất

1. Production: tối thiểu hằng quý.
2. Non production có dữ liệu nhạy cảm: tối thiểu hằng quý.
3. Non production thông thường: tối thiểu 6 tháng một lần.
4. Privileged account: tối thiểu hằng tháng hoặc theo chính sách bảo mật của tổ chức.

### 8.2. Nội dung review

1. Danh sách user.
2. Danh sách role.
3. Danh sách quyền trực tiếp.
4. Danh sách service account.
5. Danh sách privileged account.
6. Tài khoản không đăng nhập trong thời gian dài.
7. Quyền tạm thời quá hạn.
8. Quyền không còn owner.

## 9. Quy định với production

1. Không cấp quyền write production cho mục đích truy vấn thông thường.
2. Không cấp quyền admin production cho developer trừ khi có phê duyệt đặc biệt và thời hạn.
3. Truy vấn dữ liệu production phục vụ phân tích phải qua read only role nếu được phê duyệt.
4. Export dữ liệu production phải tuân thủ Data Lifecycle Management Policy và Database Security Policy.
5. Mọi thay đổi quyền production phải được audit.

## 10. Evidence bắt buộc

1. Ticket ID.
2. Người yêu cầu.
3. Người phê duyệt.
4. User hoặc service account.
5. Database và môi trường.
6. Quyền được cấp hoặc thu hồi.
7. Thời hạn nếu có.
8. Lệnh hoặc công cụ thực hiện.
9. Kết quả kiểm tra sau thực hiện.

## 11. Ngoại lệ

Ngoại lệ phải có:

1. Lý do nghiệp vụ.
2. Đánh giá rủi ro.
3. Thời hạn.
4. Approval của service owner.
5. Approval của security nếu liên quan production hoặc dữ liệu nhạy cảm.
6. Kế hoạch thu hồi hoặc đưa về trạng thái chuẩn.

## 12. Chỉ số tuân thủ

1. Số quyền production được cấp mới.
2. Số quyền tạm thời quá hạn.
3. Số privileged account.
4. Tỷ lệ access review hoàn thành đúng hạn.
5. Số tài khoản không có owner.
6. Số tài khoản không sử dụng nhưng chưa thu hồi.
7. Số ngoại lệ access control còn hiệu lực.
