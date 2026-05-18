# Database Access Control Policy

## Thông tin tài liệu

| Trường | Giá trị |
|--------|---------|
| Mã tài liệu | DBA-POL-003 |
| Loại tài liệu | Policy |
| Mức ưu tiên triển khai | 3 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Security Lead |
| Approver | Service Owner |
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
| 0.2 | 2026-05-18 | DBA Team | Bổ sung JIT access, SoD, inactive account, metadata |

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

Tài khoản đại diện cho cá nhân. Dùng cho truy cập có kiểm soát, hỗ trợ điều tra và thao tác được phê duyệt.

### 4.2. Service account

Tài khoản ứng dụng hoặc automation sử dụng. Phải có owner, secret management và phạm vi quyền rõ ràng.

### 4.3. Privileged account

Tài khoản có quyền quản trị cao. Phải được kiểm soát chặt, audit đầy đủ và review thường xuyên.

### 4.4. Break glass account

Tài khoản dùng trong trường hợp khẩn cấp. Mọi lần sử dụng phải được post review.

## 5. Just-In-Time access

1. Quyền production cao nên được cấp theo mô hình Just-In-Time (JIT) nếu có công cụ hỗ trợ.
2. JIT access chỉ kích hoạt khi cần, tự động thu hồi sau thời gian định trước.
3. Mỗi JIT request phải có ticket, lý do và thời hạn.
4. JIT phải có audit đầy đủ: thời gian kích hoạt, thời gian thu hồi, thao tác trong phiên.
5. Nếu chưa có công cụ JIT, phải dùng quy trình quyền tạm thời với DBA thu hồi thủ công.

## 6. Separation of Duties

1. Người review change không nên là người deploy change trên production với change rủi ro cao.
2. Người approve quyền không nên là người thực hiện cấp quyền với quyền nhạy cảm.
3. DBA không nên tự approve quyền cho chính mình trên production.
4. Nếu team nhỏ không đủ người để tách hoàn toàn, phải có compensating control như audit log review bởi DBA Lead.

## 7. Quy định cấp quyền

1. Request phải nêu rõ user, database, môi trường, quyền cần cấp, lý do và thời hạn.
2. Quyền production phải được service owner hoặc security phê duyệt.
3. DBA chỉ cấp quyền sau khi ticket đầy đủ.
4. DBA phải kiểm tra quyền sau khi cấp.
5. DBA phải lưu evidence gồm approval, lệnh đã dùng và kết quả kiểm tra.

## 8. Quy định quyền tạm thời

1. Quyền tạm thời phải có ngày hết hạn.
2. Quyền tạm thời phải được thu hồi tự động nếu có công cụ hỗ trợ.
3. Nếu chưa tự động hóa, DBA phải có lịch review và thu hồi.
4. Quyền tạm thời quá hạn phải được báo cáo.

## 9. Quy định thu hồi quyền

Quyền phải được thu hồi khi:

1. Người dùng chuyển vị trí.
2. Người dùng rời dự án.
3. Ứng dụng decommission.
4. Service account không còn dùng.
5. Quyền cấp sai phạm vi.
6. Quyền tạm thời hết hạn.
7. Security yêu cầu thu hồi.

## 10. Inactive account

1. Tài khoản không login trong 90 ngày phải được flag để review.
2. Tài khoản không login trong 180 ngày phải được disable trừ khi có phê duyệt ngoại lệ.
3. Service account không có traffic trong 90 ngày phải được xác nhận với owner.
4. DBA nên chạy báo cáo inactive account định kỳ hằng tháng.

## 11. Access review

### 11.1. Tần suất

1. Production: tối thiểu hằng quý.
2. Non production có dữ liệu nhạy cảm: tối thiểu hằng quý.
3. Non production thông thường: tối thiểu 6 tháng một lần.
4. Privileged account: tối thiểu hằng tháng.

### 11.2. Nội dung review

1. Danh sách user.
2. Danh sách role.
3. Danh sách quyền trực tiếp.
4. Danh sách service account.
5. Danh sách privileged account.
6. Tài khoản không đăng nhập trong thời gian dài.
7. Quyền tạm thời quá hạn.
8. Quyền không còn owner.

## 12. Quy định với production

1. Không cấp quyền write production cho mục đích truy vấn thông thường.
2. Không cấp quyền admin production cho developer trừ khi có phê duyệt đặc biệt và thời hạn.
3. Truy vấn dữ liệu production phục vụ phân tích phải qua read only role.
4. Export dữ liệu production phải tuân thủ DBA-POL-001 và DBA-POL-002.
5. Mọi thay đổi quyền production phải được audit.

## 13. Evidence bắt buộc

1. Ticket ID.
2. Người yêu cầu.
3. Người phê duyệt.
4. User hoặc service account.
5. Database và môi trường.
6. Quyền được cấp hoặc thu hồi.
7. Thời hạn nếu có.
8. Lệnh hoặc công cụ thực hiện.
9. Kết quả kiểm tra sau thực hiện.

## 14. Ngoại lệ

Ngoại lệ phải có lý do nghiệp vụ, đánh giá rủi ro, thời hạn, approval của service owner, approval của security nếu liên quan production, và kế hoạch đưa về trạng thái chuẩn.

## 15. Chỉ số tuân thủ

1. Số quyền production được cấp mới.
2. Số quyền tạm thời quá hạn.
3. Số privileged account.
4. Tỷ lệ access review hoàn thành đúng hạn.
5. Số tài khoản không có owner.
6. Số tài khoản không sử dụng nhưng chưa thu hồi.
7. Số ngoại lệ access control còn hiệu lực.
8. Số inactive account chưa xử lý.

## 16. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-POL-002 | Database Security Policy | Nguyên tắc bảo mật tổng thể |
| DBA-POL-006 | Database Audit and Compliance Policy | Audit access event |
| DBA-OM-003 | DBA RACI Matrix | Trách nhiệm cấp quyền |
