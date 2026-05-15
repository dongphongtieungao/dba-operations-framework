---
status: draft
framework: DBA Operations Framework
document_group: Templates and Metrics
created_for: DBA professional operating system
language: vi
---

# Template Emergency Change

## 1. Thông tin tài liệu

| Trường | Giá trị |
|---|---|
| Mã tài liệu | TPL-DBA-046 |
| Tên tài liệu | Template Emergency Change |
| Nhóm tài liệu | Template and Metrics |
| Mức ưu tiên | 2 |
| Mục đích | Chuẩn hóa thay đổi khẩn cấp |
| Trạng thái | Draft |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Service Owner, Infra Lead, Security Lead khi liên quan |
| Chu kỳ rà soát | Hằng quý hoặc sau mỗi sự cố lớn, thay đổi quy trình, thay đổi công cụ |

## 2. Mục đích

Tài liệu này chuẩn hóa cách ghi nhận và kiểm soát thay đổi khẩn cấp trong vận hành database. Emergency Change chỉ dùng khi có sự cố hoặc rủi ro tức thời cần hành động nhanh để bảo vệ dịch vụ, dữ liệu hoặc an toàn hệ thống.

## 3. Điều kiện sử dụng Emergency Change

| Điều kiện | Mô tả |
|---|---|
| Sự cố production nghiêm trọng | Dịch vụ ngừng hoạt động, mất kết nối database, lỗi nghiêm trọng ảnh hưởng người dùng |
| Rủi ro mất dữ liệu | Cần restore, failover, khóa thao tác hoặc cô lập database để giảm thiểu thiệt hại |
| Rủi ro bảo mật tức thời | Lộ quyền, truy cập bất thường, tài khoản bị nghi ngờ compromise |
| Không đủ thời gian đi quy trình RFC thường | Chờ RFC thường sẽ làm tăng mức độ thiệt hại |

Emergency Change không được dùng để né quy trình phê duyệt cho thay đổi đã biết trước.

## 4. Mẫu Emergency Change

| Trường | Nội dung cần điền | Bắt buộc |
|---|---|---:|
| Emergency Change ID | Mã change khẩn cấp | Có |
| Related incident | Mã incident liên quan | Có |
| Người tạo change | Họ tên, team | Có |
| Người thực hiện | Họ tên, team | Có |
| Người approve khẩn cấp | Service owner, duty manager, DBA lead hoặc người được ủy quyền | Có |
| Thời điểm phát hiện | Kèm timezone | Có |
| Thời điểm bắt đầu hành động | Kèm timezone | Có |
| Hệ thống bị ảnh hưởng | Application, service, database, instance | Có |
| Môi trường | Production, DR, Staging, UAT, Development | Có |
| DBMS | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB | Có |
| Mức độ nghiêm trọng | P1, P2, P3 hoặc theo chuẩn nội bộ | Có |
| Lý do khẩn cấp | Vì sao không thể chờ RFC thường | Có |
| Mục tiêu hành động | Giảm downtime, bảo vệ dữ liệu, phục hồi dịch vụ, cô lập rủi ro | Có |

## 5. Mô tả tình huống khẩn cấp

| Nội dung | Mô tả |
|---|---|
| Triệu chứng chính |  |
| Tác động hiện tại |  |
| Số lượng user, batch, service bị ảnh hưởng |  |
| Rủi ro nếu không hành động ngay |  |
| Evidence ban đầu | Alert, log, dashboard, APM, ticket, screenshot |
| Giả định kỹ thuật tại thời điểm quyết định |  |

## 6. Hành động khẩn cấp được đề xuất

| Bước | Hành động | Người thực hiện | Mục tiêu | Rủi ro | Evidence |
|---:|---|---|---|---|---|
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |

## 7. Kiểm soát tối thiểu trước khi thực hiện

| # | Kiểm soát | Kết quả | Ghi chú |
|---:|---|---|---|
| 1 | Đã xác nhận đúng hệ thống và đúng môi trường | Pass, Fail |  |
| 2 | Đã có approval khẩn cấp | Pass, Fail |  |
| 3 | Đã xác định tác động nếu thao tác thất bại | Pass, Fail |  |
| 4 | Đã thu thập evidence trước thao tác nếu còn khả thi | Pass, Fail |  |
| 5 | Đã xác định kênh thông báo cho các bên liên quan | Pass, Fail |  |
| 6 | Đã có phương án rollback hoặc recovery tối thiểu | Pass, Fail, N/A |  |

## 8. Kết quả thực hiện

| Trường | Nội dung |
|---|---|
| Thời điểm hoàn tất thao tác |  |
| Kết quả đạt được |  |
| Dịch vụ đã phục hồi chưa | Yes, No, Partially |
| Dữ liệu có bị ảnh hưởng không | Yes, No, Unknown |
| Rủi ro còn lại |  |
| Monitoring sau thao tác |  |
| Người xác nhận |  |

## 9. Evidence bắt buộc

| Evidence | Bắt buộc | Ghi chú |
|---|---:|---|
| Alert hoặc incident ban đầu | Có |  |
| Approval khẩn cấp | Có |  |
| Lệnh, script, job hoặc pipeline đã chạy | Có | Không ghi secret |
| Output hoặc log thực hiện | Có |  |
| Dashboard trước và sau | Có nếu có thể |  |
| Kết quả kiểm tra sau thao tác | Có |  |
| Timeline sự cố | Có | Có thể bổ sung sau trong PIR |

## 10. Post review bắt buộc

Emergency Change phải được review sau khi ổn định dịch vụ. Nội dung review tối thiểu:

1. Vì sao phát sinh emergency change.
2. Emergency change có hợp lệ không.
3. Có thể phòng ngừa bằng monitoring, automation, capacity planning hoặc SOP tốt hơn không.
4. Có cần tạo RFC chính thức để chuẩn hóa cấu hình sau emergency không.
5. Có cần cập nhật SOP, runbook, alert hoặc policy không.
6. Có cần tạo Post Incident Review không.

## 11. Mapping tài liệu liên quan

| Tình huống | Tài liệu liên quan |
|---|---|
| Emergency restore | SOP Restore Operation, Backup and Restore Policy, Template Operation Evidence |
| Emergency failover | SOP HA Failover and Switchover, Database HA and DR Policy, Template Failover Drill Report nếu cần |
| Emergency permission revoke | SOP User and Permission Management, Database Access Control Policy |
| Emergency performance mitigation | SOP Performance Troubleshooting, SOP Blocking and Deadlock Handling |
| Incident lớn | Template Post Incident Review |

## 99. Lịch sử thay đổi

| Version | Ngày | Người cập nhật | Nội dung thay đổi |
|---|---|---|---|
| 0.1 | YYYY-MM-DD | DBA Team | Khởi tạo bản nháp |
