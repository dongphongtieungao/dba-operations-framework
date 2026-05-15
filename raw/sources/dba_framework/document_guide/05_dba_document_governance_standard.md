# DBA Document Governance Standard

## Thông tin tài liệu

1. Mã tài liệu: DBA DG 001
2. Loại tài liệu: Standard
3. Mức ưu tiên triển khai: 3
4. Owner đề xuất: DBA Team
5. Trạng thái: Draft
6. Phiên bản: 0.1
7. Phạm vi áp dụng: SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid
8. Chu kỳ review: 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật

## 1. Mục đích

Tài liệu này quy định cách tạo, đặt mã, review, phê duyệt, cập nhật, archive và sử dụng tài liệu trong DBA Operations Framework. Mục tiêu là đảm bảo tài liệu luôn có owner, có trạng thái rõ ràng, có version và có giá trị vận hành thực tế.

## 2. Phạm vi

Áp dụng cho tất cả tài liệu DBA gồm:

1. Framework.
2. Operating model.
3. Policy.
4. Standard.
5. SOP.
6. Runbook.
7. Template.
8. Report.
9. Appendix theo từng DBMS.
10. Knowledge base note.

## 3. Quy tắc mã tài liệu

### 3.1. Nhóm mã

1. DBA OF: Framework overview.
2. DBA OM: Operating model.
3. DBA POL: Policy.
4. DBA STD: Standard.
5. DBA SOP: Standard operating procedure.
6. DBA RBK: Runbook.
7. DBA TMP: Template.
8. DBA RPT: Report.
9. DBA APP: DBMS appendix.
10. DBA KB: Knowledge base.

### 3.2. Ví dụ mã

1. DBA POL 001: Backup and Restore Policy.
2. DBA SOP 003: Restore Operation.
3. DBA RBK 002: Backup Verification Runbook.
4. DBA TMP 004: Operation Evidence Template.

## 4. Metadata bắt buộc

Mỗi tài liệu phải có các thông tin sau:

1. Mã tài liệu.
2. Tên tài liệu.
3. Loại tài liệu.
4. Owner.
5. Reviewer.
6. Approver.
7. Trạng thái.
8. Phiên bản.
9. Ngày tạo.
10. Ngày review gần nhất.
11. Ngày review tiếp theo.
12. Phạm vi áp dụng.
13. Tài liệu liên quan.
14. Evidence liên quan nếu có.
15. Lịch sử thay đổi.

## 5. Trạng thái tài liệu

### 5.1. Draft

Tài liệu đang soạn, chưa được áp dụng chính thức.

### 5.2. In review

Tài liệu đang được review bởi reviewer hoặc bên liên quan.

### 5.3. Approved

Tài liệu đã được phê duyệt nhưng có thể chưa áp dụng rộng.

### 5.4. Active

Tài liệu đang có hiệu lực vận hành.

### 5.5. Deprecated

Tài liệu không còn khuyến nghị sử dụng nhưng vẫn giữ để tham khảo trong giai đoạn chuyển tiếp.

### 5.6. Archived

Tài liệu đã ngừng sử dụng và chỉ lưu để truy vết lịch sử.

## 6. Cấu trúc tối thiểu theo loại tài liệu

### 6.1. Policy

1. Mục đích.
2. Phạm vi.
3. Nguyên tắc.
4. Quy định bắt buộc.
5. Ngoại lệ.
6. Trách nhiệm.
7. Evidence và kiểm toán.
8. Chỉ số tuân thủ.
9. Chu kỳ review.

### 6.2. Standard

1. Mục đích.
2. Phạm vi.
3. Quy chuẩn.
4. Ví dụ đúng.
5. Ví dụ sai hoặc anti pattern.
6. Checklist áp dụng.
7. Liên kết SOP hoặc runbook.

### 6.3. SOP

1. Mục đích.
2. Phạm vi.
3. Điều kiện tiên quyết.
4. Vai trò liên quan.
5. Các bước thực hiện.
6. Kiểm tra sau thực hiện.
7. Rollback hoặc mitigation.
8. Rủi ro và cảnh báo.
9. Evidence bắt buộc.
10. Escalation.

### 6.4. Runbook

1. Mục đích.
2. Phạm vi.
3. Input.
4. Output.
5. Điều kiện chạy.
6. Câu lệnh hoặc pipeline.
7. Validation.
8. Rollback.
9. Log và evidence.
10. Quyền cần thiết.

### 6.5. Template

1. Mục đích.
2. Khi nào sử dụng.
3. Các trường bắt buộc.
4. Cách điền.
5. Ví dụ mẫu.
6. Quy tắc lưu trữ.

## 7. Quy trình tạo tài liệu mới

1. Xác định nhu cầu tài liệu.
2. Chọn loại tài liệu.
3. Đặt mã tài liệu.
4. Chỉ định owner.
5. Soạn bản Draft.
6. Review với bên liên quan.
7. Cập nhật theo comment.
8. Phê duyệt.
9. Chuyển trạng thái Active.
10. Cập nhật index hoặc knowledge map.

## 8. Quy trình cập nhật tài liệu

1. Tạo yêu cầu cập nhật hoặc ghi nhận từ sự cố, audit, change hoặc cải tiến.
2. Owner cập nhật nội dung.
3. Reviewer kiểm tra tính chính xác.
4. Approver phê duyệt nếu thay đổi ảnh hưởng quy trình chính thức.
5. Cập nhật version.
6. Ghi lịch sử thay đổi.
7. Thông báo cho team liên quan.

## 9. Quy tắc version

1. Thay đổi nhỏ về câu chữ tăng patch version.
2. Thay đổi quy trình, step hoặc checklist tăng minor version.
3. Thay đổi chính sách, phạm vi hoặc trách nhiệm tăng major version.
4. Không chỉnh tài liệu Active mà không cập nhật lịch sử thay đổi.

## 10. Quy tắc archive

Tài liệu được archive khi:

1. Công nghệ không còn sử dụng.
2. Quy trình được thay thế.
3. Dịch vụ bị decommission.
4. Policy mới đã thay thế hoàn toàn.
5. Owner và approver xác nhận archive.

## 11. Chỉ số đánh giá

1. Tỷ lệ tài liệu có owner.
2. Tỷ lệ tài liệu được review đúng hạn.
3. Tỷ lệ SOP có evidence template.
4. Tỷ lệ runbook có validation.
5. Số tài liệu Active quá hạn review.
6. Số sự cố do tài liệu lỗi thời hoặc thiếu tài liệu.
