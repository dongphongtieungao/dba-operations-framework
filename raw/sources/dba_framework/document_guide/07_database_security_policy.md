# Database Security Policy

## Thông tin tài liệu

1. Mã tài liệu: DBA POL 002
2. Loại tài liệu: Policy
3. Mức ưu tiên triển khai: 2
4. Owner đề xuất: DBA Team
5. Trạng thái: Draft
6. Phiên bản: 0.1
7. Phạm vi áp dụng: SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid
8. Chu kỳ review: 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật

## 1. Mục đích

Chính sách này thiết lập các nguyên tắc bảo mật database nhằm bảo vệ tính bí mật, toàn vẹn và sẵn sàng của dữ liệu. Chính sách tập trung vào least privilege, encryption, key management, audit, data masking và kiểm soát tài khoản đặc quyền.

## 2. Phạm vi

Áp dụng cho tất cả database production và non production, bao gồm SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng managed database.

## 3. Nguyên tắc bảo mật

1. Least privilege là nguyên tắc mặc định.
2. Không cấp quyền cao thường trực nếu không có phê duyệt.
3. Mọi truy cập production phải có khả năng truy vết.
4. Dữ liệu nhạy cảm phải được mã hóa và audit.
5. Non production không được dùng dữ liệu thật nhạy cảm nếu chưa masking.
6. Service account phải có owner và phạm vi quyền rõ ràng.
7. Tài khoản không sử dụng phải bị vô hiệu hóa hoặc xóa theo quy trình.

## 4. Quản lý tài khoản đặc quyền

1. Quyền SYSDBA, SUPERUSER, sysadmin, db owner hoặc quyền tương đương chỉ được cấp cho nhóm được phê duyệt.
2. Quyền đặc quyền trên production phải có kiểm soát truy cập, audit và review định kỳ.
3. Không sử dụng tài khoản dùng chung nếu không có cơ chế kiểm soát và truy vết cá nhân.
4. Break glass account phải được quản lý riêng, có mật khẩu bảo vệ, có audit và chỉ dùng trong tình huống khẩn cấp.
5. Sau khi dùng break glass account phải lập post review.

## 5. Service account

1. Mỗi service account phải có owner.
2. Service account chỉ được cấp quyền cần thiết cho chức năng ứng dụng.
3. Service account không được dùng cho thao tác cá nhân.
4. Mật khẩu hoặc secret của service account phải lưu trong secret manager hoặc cơ chế quản lý bí mật được phê duyệt.
5. Service account phải được rotate secret định kỳ hoặc khi có nghi ngờ lộ lọt.
6. Khi ứng dụng decommission, service account phải được thu hồi.

## 6. Encryption

### 6.1. Encryption at rest

1. Database chứa P2 hoặc P3 phải sử dụng mã hóa dữ liệu lưu trữ nếu DBMS hoặc platform hỗ trợ.
2. Backup chứa dữ liệu P2 hoặc P3 phải được mã hóa.
3. File export chứa dữ liệu nhạy cảm phải được mã hóa hoặc bảo vệ bằng cơ chế tương đương.
4. Key sử dụng cho encryption phải được quản lý theo chính sách key management.

### 6.2. Encryption in transit

1. Kết nối database production phải sử dụng TLS nếu DBMS hoặc platform hỗ trợ.
2. Không cho phép truyền thông tin xác thực qua kết nối không mã hóa.
3. Certificate hết hạn phải được phát hiện trước khi gây sự cố.
4. Thay đổi certificate phải có kế hoạch rollback.

## 7. Key management

1. Key phải được lưu trong hệ thống quản lý khóa được phê duyệt.
2. Không lưu key trong script, source code hoặc tài liệu không bảo vệ.
3. Key phải có owner.
4. Key rotation phải có kế hoạch kiểm tra ứng dụng.
5. Việc xóa hoặc vô hiệu hóa key phải được đánh giá ảnh hưởng tới restore và truy xuất dữ liệu cũ.

## 8. Data masking

1. Dữ liệu P2 và P3 khi đưa sang non production phải được masking trừ khi có phê duyệt ngoại lệ.
2. Masking rule phải đảm bảo dữ liệu không thể suy ngược dễ dàng về dữ liệu gốc.
3. Dữ liệu sau masking phải vẫn đủ tính đại diện để test nếu cần.
4. Kết quả masking phải được kiểm tra và lưu evidence.
5. Không export dữ liệu production cho mục đích test nếu chưa xác nhận masking hoặc phê duyệt ngoại lệ.

## 9. Audit log

1. Phải audit thay đổi quyền.
2. Phải audit thay đổi schema production.
3. Phải audit truy cập dữ liệu nhạy cảm nếu DBMS hoặc platform hỗ trợ.
4. Phải audit login failed bất thường.
5. Audit log phải được bảo vệ khỏi sửa xóa trái phép.
6. Audit log phải có retention theo Database Audit and Compliance Policy.

## 10. Network security

1. Database production không được mở truy cập công khai nếu không có phê duyệt đặc biệt.
2. Chỉ cho phép truy cập từ subnet, security group, firewall rule hoặc private endpoint được phê duyệt.
3. Administrative access phải đi qua bastion, jump host, VPN hoặc cơ chế kiểm soát tương đương.
4. Mọi thay đổi network rule liên quan database phải có ticket và evidence.

## 11. Security exception

Ngoại lệ bảo mật chỉ được chấp nhận khi:

1. Có lý do nghiệp vụ rõ ràng.
2. Có đánh giá rủi ro.
3. Có biện pháp giảm thiểu.
4. Có owner chịu trách nhiệm.
5. Có ngày hết hạn.
6. Có phê duyệt của security và service owner.

## 12. Chỉ số tuân thủ

1. Số tài khoản đặc quyền thường trực.
2. Tỷ lệ service account có owner.
3. Tỷ lệ database production sử dụng encryption.
4. Tỷ lệ kết nối production sử dụng TLS.
5. Tỷ lệ non production database đã masking khi chứa dữ liệu nhạy cảm.
6. Số ngoại lệ bảo mật còn hiệu lực.
7. Số failed login bất thường chưa xử lý.
