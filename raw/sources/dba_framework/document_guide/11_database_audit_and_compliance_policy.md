# Database Audit and Compliance Policy

## Thông tin tài liệu

1. Mã tài liệu: DBA POL 006
2. Loại tài liệu: Policy
3. Mức ưu tiên triển khai: 2
4. Owner đề xuất: DBA Team
5. Trạng thái: Draft
6. Phiên bản: 0.1
7. Phạm vi áp dụng: SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid
8. Chu kỳ review: 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật

## 1. Mục đích

Chính sách này quy định nội dung cần audit, cách lưu trữ audit log, cách kiểm tra tuân thủ và cách cung cấp evidence phục vụ kiểm toán. Mục tiêu là đảm bảo các thao tác quan trọng trên database có thể truy vết và chứng minh được.

## 2. Phạm vi

Áp dụng cho audit log database, system log liên quan database, access log, change log, backup restore log, failover log, privileged activity log và evidence từ pipeline hoặc automation.

## 3. Nguyên tắc

1. Audit phải tập trung vào hành động có rủi ro cao.
2. Audit log phải bảo vệ khỏi sửa hoặc xóa trái phép.
3. Audit log phải có retention rõ ràng.
4. Audit phải đủ thông tin để xác định ai làm gì, khi nào, ở đâu và kết quả ra sao.
5. Audit log không được chứa thông tin nhạy cảm quá mức cần thiết.
6. Evidence cho audit phải liên kết được với ticket hoặc change record.

## 4. Sự kiện bắt buộc audit

### 4.1. Access event

1. Login thành công vào production nếu DBMS hỗ trợ và policy yêu cầu.
2. Failed login bất thường.
3. Truy cập bằng privileged account.
4. Truy cập dữ liệu nhạy cảm nếu DBMS hoặc platform hỗ trợ.

### 4.2. Permission event

1. Tạo user.
2. Xóa user.
3. Cấp quyền.
4. Thu hồi quyền.
5. Thay đổi role.
6. Thay đổi owner.
7. Thay đổi privileged account.

### 4.3. Schema event

1. Create, alter, drop table.
2. Create, alter, drop index.
3. Create, alter, drop procedure, function, trigger hoặc package.
4. Thay đổi schema ảnh hưởng production.

### 4.4. Data operation event

1. Bulk update hoặc bulk delete production nếu có rủi ro.
2. Data correction.
3. Data migration.
4. Export dữ liệu nhạy cảm.
5. Purge hoặc archive dữ liệu.

### 4.5. Backup restore event

1. Backup ad hoc.
2. Backup failure.
3. Restore.
4. Restore drill.
5. Xóa backup trước retention.
6. Thay đổi backup policy.

### 4.6. HA DR event

1. Failover.
2. Switchover.
3. Promote replica.
4. Thay đổi replication.
5. DR drill.
6. Thay đổi endpoint hoặc connection string liên quan database.

## 5. Retention audit log

1. Retention phải phù hợp yêu cầu pháp lý, bảo mật và audit nội bộ.
2. Audit log production phải lưu tối thiểu theo yêu cầu tổ chức.
3. Audit log liên quan P1, P2 hoặc dữ liệu nhạy cảm phải được bảo quản cho đến khi kết thúc review hoặc audit.
4. Audit log hết retention phải được xóa theo quy trình.
5. Không xóa audit log đang liên quan sự cố, tranh chấp hoặc kiểm toán.

## 6. Bảo vệ audit log

1. Audit log phải lưu ở vị trí có kiểm soát quyền.
2. Người có quyền thao tác database không nên có quyền xóa audit log nếu hệ thống cho phép tách quyền.
3. Audit log quan trọng nên được chuyển ra hệ thống log tập trung.
4. Log phải có timestamp nhất quán.
5. Thời gian hệ thống phải được đồng bộ.

## 7. Compliance review

### 7.1. Review định kỳ

1. Review privileged account.
2. Review quyền production.
3. Review backup và restore evidence.
4. Review failover hoặc DR drill evidence.
5. Review change record.
6. Review exception còn hiệu lực.

### 7.2. Review sau sự cố

1. Thu thập audit log liên quan.
2. Xác định timeline.
3. Xác định hành động người dùng hoặc hệ thống.
4. Kiểm tra thao tác ngoài quy trình nếu có.
5. Đưa kết quả vào PIR.

## 8. Evidence phục vụ audit

Evidence tối thiểu gồm:

1. Ticket ID hoặc change ID.
2. Người yêu cầu.
3. Người phê duyệt.
4. Người thực hiện.
5. Thời gian.
6. Hệ thống ảnh hưởng.
7. Database ảnh hưởng.
8. Lệnh, script, pipeline hoặc công cụ đã sử dụng.
9. Output.
10. Kết quả kiểm tra sau thực hiện.
11. Link tới log nếu có.

## 9. Ngoại lệ

Ngoại lệ audit chỉ được chấp nhận khi:

1. DBMS hoặc platform không hỗ trợ audit yêu cầu.
2. Việc bật audit gây ảnh hưởng nghiêm trọng đã được chứng minh.
3. Có biện pháp thay thế.
4. Có phê duyệt của security và service owner.
5. Có thời hạn và kế hoạch xử lý.

## 10. Chỉ số tuân thủ

1. Tỷ lệ database production có audit baseline.
2. Tỷ lệ privileged activity có audit.
3. Tỷ lệ change production có evidence đầy đủ.
4. Số audit exception còn hiệu lực.
5. Số sự kiện failed login bất thường chưa xử lý.
6. Số lần xóa hoặc thiếu audit log không hợp lệ.
7. Tỷ lệ compliance review hoàn thành đúng hạn.
