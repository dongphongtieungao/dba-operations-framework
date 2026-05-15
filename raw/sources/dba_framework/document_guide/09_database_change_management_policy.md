# Database Change Management Policy

## Thông tin tài liệu

1. Mã tài liệu: DBA POL 004
2. Loại tài liệu: Policy
3. Mức ưu tiên triển khai: 2
4. Owner đề xuất: DBA Team
5. Trạng thái: Draft
6. Phiên bản: 0.1
7. Phạm vi áp dụng: SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid
8. Chu kỳ review: 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật

## 1. Mục đích

Chính sách này quy định cách quản lý thay đổi trên database nhằm giảm rủi ro gián đoạn dịch vụ, mất dữ liệu, suy giảm hiệu năng hoặc vi phạm bảo mật. Mọi thay đổi production phải có kế hoạch, review, phê duyệt, kiểm tra sau thay đổi và evidence.

## 2. Phạm vi

Áp dụng cho các thay đổi liên quan đến:

1. Schema.
2. Index.
3. Stored procedure, function, trigger hoặc package.
4. Data migration.
5. Database configuration.
6. Backup policy.
7. HA và replication.
8. Access control.
9. Monitoring và alerting.
10. Upgrade, patch hoặc parameter change.

## 3. Loại thay đổi

### 3.1. Standard change

Thay đổi đã được chuẩn hóa, rủi ro thấp, có SOP hoặc runbook đã được phê duyệt trước. Ví dụ: cấp quyền read only theo mẫu, tạo database non production theo template.

### 3.2. Normal change

Thay đổi thông thường cần review và approval trước khi thực hiện. Ví dụ: thay đổi schema production, tạo index mới trên bảng lớn, thay đổi backup schedule.

### 3.3. Emergency change

Thay đổi khẩn cấp để xử lý sự cố nghiêm trọng, ngăn mất dữ liệu hoặc khôi phục dịch vụ. Emergency change phải có post review sau khi ổn định.

## 4. Nguyên tắc quản lý thay đổi

1. Không thay đổi production nếu không có ticket hoặc emergency approval.
2. Mọi change phải có mục đích rõ ràng.
3. Mọi change phải có phạm vi ảnh hưởng.
4. Mọi change phải có kế hoạch kiểm tra.
5. Mọi change rủi ro cao phải có rollback plan.
6. Mọi change production phải có evidence.
7. Change phải được thực hiện trong maintenance window nếu có nguy cơ ảnh hưởng dịch vụ.
8. Change lớn phải được peer review.

## 5. Nội dung RFC bắt buộc

1. Mã change.
2. Người yêu cầu.
3. Người thực hiện.
4. Hệ thống ảnh hưởng.
5. Database ảnh hưởng.
6. Môi trường.
7. Mô tả thay đổi.
8. Lý do thay đổi.
9. Phạm vi ảnh hưởng.
10. Rủi ro.
11. Kế hoạch thực hiện.
12. Kế hoạch kiểm tra.
13. Kế hoạch rollback.
14. Maintenance window.
15. Người phê duyệt.
16. Evidence cần đính kèm.

## 6. Review thay đổi database

DBA phải review các điểm sau trước khi approve hoặc thực hiện:

1. Script có idempotent hay không.
2. Script có transaction phù hợp hay không.
3. Có nguy cơ lock bảng lớn hay không.
4. Có nguy cơ log growth hoặc WAL growth hay không.
5. Có ảnh hưởng replication hay không.
6. Có ảnh hưởng backup hoặc restore hay không.
7. Có ảnh hưởng quyền truy cập hoặc bảo mật hay không.
8. Có ảnh hưởng query plan hoặc index hay không.
9. Có rollback thực tế hay chỉ mô tả hình thức.
10. Có kế hoạch validation sau thay đổi hay không.

## 7. Điều kiện triển khai production

1. RFC đã được phê duyệt.
2. Script đã được review.
3. Backup hoặc restore point đã được xác nhận nếu thay đổi rủi ro cao.
4. Rollback plan đã được chuẩn bị.
5. Maintenance window đã được xác nhận nếu cần.
6. Người thực hiện có quyền hợp lệ.
7. Bên ứng dụng sẵn sàng kiểm tra.
8. Monitoring sẵn sàng theo dõi trong và sau thay đổi.

## 8. Emergency change

1. Emergency change được phép khi hệ thống production đang bị ảnh hưởng nghiêm trọng hoặc có nguy cơ mất dữ liệu.
2. Phải có approval tối thiểu từ DBA Lead hoặc Service Owner theo kênh khẩn cấp.
3. DBA phải lưu lại timeline và evidence.
4. Sau khi xử lý, phải tạo hoặc cập nhật emergency change record.
5. PIR bắt buộc nếu emergency change liên quan P1 hoặc P2.

## 9. Rollback

1. Rollback phải được kiểm tra tính khả thi trước khi change.
2. Với thay đổi dữ liệu lớn, rollback bằng script ngược có thể không đủ, cần phương án restore hoặc restore object level nếu có.
3. Không được ghi rollback plan chung chung như rollback nếu lỗi.
4. Rollback plan phải chỉ rõ điều kiện kích hoạt rollback.
5. Rollback phải có người quyết định.

## 10. Evidence bắt buộc

1. RFC approval.
2. Script version.
3. Người thực hiện.
4. Thời gian bắt đầu và kết thúc.
5. Output của script hoặc pipeline.
6. Kết quả kiểm tra sau thay đổi.
7. Monitoring screenshot hoặc metric nếu có.
8. Rollback evidence nếu rollback được thực hiện.
9. Xác nhận của application owner nếu cần.

## 11. Chỉ số tuân thủ

1. Change success rate.
2. Rollback rate.
3. Emergency change ratio.
4. Change gây incident.
5. Change không có evidence đầy đủ.
6. Change không có rollback plan.
7. Change triển khai ngoài maintenance window không được phê duyệt.
