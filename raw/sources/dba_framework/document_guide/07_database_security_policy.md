# Database Security Policy

## Thông tin tài liệu

| Trường | Giá trị |
|--------|---------|
| Mã tài liệu | DBA-POL-002 |
| Loại tài liệu | Policy |
| Mức ưu tiên triển khai | 2 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Security Lead |
| Approver | Service Owner, Security Lead |
| Trạng thái | Draft |
| Phiên bản | 0.2 |
| Ngày tạo | 2026-05-18 |
| Ngày review gần nhất | 2026-05-18 |
| Ngày review tiếp theo | 2026-11-18 |
| Phạm vi áp dụng | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng |

### Lịch sử thay đổi

| Phiên bản | Ngày | Người thay đổi | Mô tả |
|-----------|------|----------------|-------|
| 0.1 | 2026-05-18 | DBA Team | Bản draft đầu tiên |
| 0.2 | 2026-05-18 | DBA Team | Bổ sung password policy, vulnerability scanning, SQL injection, metadata |

## 1. Mục đích

Chính sách này thiết lập các nguyên tắc bảo mật database nhằm bảo vệ tính bí mật, toàn vẹn và sẵn sàng của dữ liệu.

## 2. Phạm vi

Áp dụng cho tất cả database production và non production.

## 3. Nguyên tắc bảo mật

1. Least privilege là nguyên tắc mặc định.
2. Không cấp quyền cao thường trực nếu không có phê duyệt.
3. Mọi truy cập production phải có khả năng truy vết.
4. Dữ liệu nhạy cảm phải được mã hóa và audit.
5. Non production không được dùng dữ liệu thật nhạy cảm nếu chưa masking.
6. Service account phải có owner và phạm vi quyền rõ ràng.
7. Tài khoản không sử dụng phải bị vô hiệu hóa hoặc xóa theo quy trình.

## 4. Quản lý tài khoản đặc quyền

1. Quyền SYSDBA, SUPERUSER, sysadmin, db_owner chỉ được cấp cho nhóm được phê duyệt.
2. Quyền đặc quyền trên production phải có audit và review định kỳ.
3. Không sử dụng tài khoản dùng chung nếu không có cơ chế truy vết cá nhân.
4. Break glass account phải được quản lý riêng, có audit và chỉ dùng khẩn cấp.
5. Sau khi dùng break glass account phải lập post review.

## 5. Password policy

1. Mật khẩu database phải có độ dài tối thiểu theo quy định tổ chức, khuyến nghị ít nhất 16 ký tự cho service account.
2. Mật khẩu phải có complexity nếu DBMS hỗ trợ enforce.
3. Không lưu mật khẩu trong script, source code hoặc file cấu hình plaintext.
4. Mật khẩu phải được lưu trong secret manager hoặc vault.
5. Mật khẩu service account phải được rotate định kỳ hoặc khi nghi ngờ lộ lọt.
6. Mật khẩu DBA privileged account phải được rotate ít nhất mỗi quý.

## 6. Service account

1. Mỗi service account phải có owner.
2. Service account chỉ được cấp quyền cần thiết cho chức năng ứng dụng.
3. Service account không được dùng cho thao tác cá nhân.
4. Secret phải lưu trong secret manager được phê duyệt.
5. Secret phải được rotate định kỳ.
6. Khi ứng dụng decommission, service account phải được thu hồi.

## 7. Encryption

### 7.1. Encryption at rest

1. Database chứa P2 hoặc P3 phải sử dụng mã hóa nếu DBMS hỗ trợ.
2. Backup chứa P2 hoặc P3 phải được mã hóa.
3. File export nhạy cảm phải được mã hóa.
4. Key phải được quản lý theo chính sách key management.

### 7.2. Encryption in transit

1. Kết nối database production phải sử dụng TLS nếu DBMS hỗ trợ.
2. Không cho phép truyền thông tin xác thực qua kết nối không mã hóa.
3. Certificate hết hạn phải được phát hiện trước khi gây sự cố.
4. Thay đổi certificate phải có kế hoạch rollback.

## 8. Key management

1. Key phải được lưu trong hệ thống quản lý khóa được phê duyệt.
2. Không lưu key trong script, source code hoặc tài liệu không bảo vệ.
3. Key phải có owner.
4. Key rotation phải có kế hoạch kiểm tra ứng dụng.
5. Xóa hoặc vô hiệu hóa key phải đánh giá ảnh hưởng tới restore dữ liệu cũ.

## 9. Data masking

1. Dữ liệu P2 và P3 khi đưa sang non production phải được masking trừ khi có ngoại lệ.
2. Masking rule phải đảm bảo dữ liệu không thể suy ngược.
3. Dữ liệu sau masking phải vẫn đủ tính đại diện để test.
4. Kết quả masking phải được kiểm tra và lưu evidence.
5. Không export dữ liệu production cho test nếu chưa xác nhận masking.

## 10. Audit log

1. Phải audit thay đổi quyền.
2. Phải audit thay đổi schema production.
3. Phải audit truy cập dữ liệu nhạy cảm nếu DBMS hỗ trợ.
4. Phải audit login failed bất thường.
5. Audit log phải được bảo vệ khỏi sửa xóa trái phép.
6. Audit log phải có retention theo DBA-POL-006.

## 11. Network security

1. Database production không được mở truy cập công khai.
2. Chỉ cho phép truy cập từ subnet, security group hoặc private endpoint được phê duyệt.
3. Administrative access phải qua bastion, jump host hoặc VPN.
4. Thay đổi network rule liên quan database phải có ticket và evidence.

## 12. SQL injection prevention

1. Không cho phép dynamic SQL không kiểm soát khi kết nối production.
2. Khuyến nghị parameterized query là phương pháp mặc định.
3. DBA nên review stored procedure có dynamic SQL khi review schema change.
4. Khi phát hiện SQL injection attempt trong audit log, phải báo security team.

## 13. Vulnerability scanning

1. Database production nên được quét lỗ hổng định kỳ nếu có công cụ hỗ trợ.
2. Ví dụ: SQL Server Vulnerability Assessment, Oracle DBSAT, PostgreSQL security audit.
3. Kết quả quét phải được review và tạo action item cho finding nghiêm trọng.
4. Finding chưa xử lý phải được ghi nhận trong risk register.

## 14. Security exception

Ngoại lệ bảo mật chỉ được chấp nhận khi có lý do nghiệp vụ, đánh giá rủi ro, biện pháp giảm thiểu, owner, ngày hết hạn và phê duyệt của security và service owner.

## 15. Chỉ số tuân thủ

1. Số tài khoản đặc quyền thường trực.
2. Tỷ lệ service account có owner.
3. Tỷ lệ database production sử dụng encryption.
4. Tỷ lệ kết nối production sử dụng TLS.
5. Tỷ lệ non production đã masking khi chứa dữ liệu nhạy cảm.
6. Số ngoại lệ bảo mật còn hiệu lực.
7. Số failed login bất thường chưa xử lý.
8. Vulnerability finding chưa xử lý.

## 16. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-POL-001 | Data Lifecycle Management Policy | Data classification |
| DBA-POL-003 | Database Access Control Policy | Access control chi tiết |
| DBA-POL-006 | Database Audit and Compliance Policy | Audit log |
| DBA-OM-003 | DBA RACI Matrix | Trách nhiệm bảo mật |
