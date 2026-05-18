---
status: draft
framework: DBA Operations Framework
document_group: Templates and Metrics
doc_id: DBA-TMP-048
language: vi
---

# Template Operation Evidence

## 1. Thông tin tài liệu

| Trường | Giá trị |
|---|---|
| Mã tài liệu | DBA-TMP-048 |
| Tên tài liệu | Template Operation Evidence |
| Nhóm tài liệu | Template and Metrics |
| Mức ưu tiên | 1 |
| Mục đích | Chuẩn hóa bằng chứng vận hành |
| Trạng thái | Draft |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Service Owner, Infra Lead, Security Lead khi liên quan |
| Chu kỳ rà soát | Hằng quý hoặc sau mỗi sự cố lớn, thay đổi quy trình, thay đổi công cụ |

## 2. Mục đích

Tài liệu này chuẩn hóa cách ghi nhận evidence cho mọi thao tác DBA. Evidence giúp chứng minh thao tác được thực hiện đúng người, đúng thời điểm, đúng phạm vi, đúng quy trình, có kết quả kiểm tra và có thể audit lại khi cần.

## 3. Nguyên tắc evidence

1. Evidence phải gắn với ticket hoặc change ID.
2. Evidence phải đủ để người khác hiểu thao tác đã làm gì, trên hệ thống nào, kết quả ra sao.
3. Không ghi password, private key, token, secret hoặc dữ liệu nhạy cảm không cần thiết vào evidence.
4. Lệnh hoặc script cần được ghi lại ở mức đủ trace, nhưng phải che thông tin bí mật.
5. Với production, evidence phải lưu trong hệ thống được kiểm soát quyền truy cập.
6. Với thao tác có rủi ro, phải có evidence trước và sau thao tác.

## 4. Mẫu thông tin chung

| Trường | Nội dung cần điền | Bắt buộc |
|---|---|---:|
| Ticket ID |  | Có |
| Change ID |  | Có nếu là change |
| Operation type | Backup, Restore, Failover, User Management, Tuning, Upgrade, Other | Có |
| SOP hoặc runbook sử dụng |  | Có |
| Người thực hiện |  | Có |
| Người review hoặc approve |  | Có với production |
| Thời gian bắt đầu |  | Có |
| Thời gian kết thúc |  | Có |
| Timezone |  | Có |
| Environment | Production, DR, Staging, UAT, Development | Có |
| Application, service |  | Có |
| DBMS | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB | Có |
| Instance, database |  | Có |
| Risk level | Low, Medium, High, Critical | Có |

## 5. Evidence trước thao tác

| # | Evidence | Nội dung | File hoặc link | Kết quả |
|---:|---|---|---|---|
| 1 | Approval | Người approve, thời điểm approve |  |  |
| 2 | Backup status | Backup gần nhất, backup age, backup chain |  |  |
| 3 | HA hoặc replication status | Primary, replica, lag, role |  |  |
| 4 | Capacity status | Disk, log, WAL, temp, tablespace |  |  |
| 5 | Monitoring snapshot | Dashboard trước thao tác |  |  |
| 6 | Script review | Link commit, merge request, peer review |  |  |
| 7 | Pre check query output | Output đã che dữ liệu nhạy cảm nếu cần |  |  |

## 6. Evidence trong quá trình thực hiện

| Bước | Lệnh, script hoặc job | Người thực hiện | Thời gian | Output chính | Ghi chú |
|---:|---|---|---|---|---|
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |

## 7. Evidence sau thao tác

| # | Evidence | Kết quả mong muốn | Kết quả thực tế | File hoặc link |
|---:|---|---|---|---|
| 1 | Database status | Online, healthy |  |  |
| 2 | Application connection | Kết nối thành công |  |  |
| 3 | Error log | Không có lỗi mới nghiêm trọng |  |  |
| 4 | Monitoring snapshot | Metric ổn định |  |  |
| 5 | Backup, restore, HA hoặc replication check | Pass theo SOP liên quan |  |  |
| 6 | Validation query | Pass |  |  |
| 7 | Requester confirmation | Confirmed hoặc N/A |  |  |

## 8. Kết quả và kết luận

| Trường | Nội dung |
|---|---|
| Operation result | Success, Partial Success, Failed, Rolled Back |
| Kết quả chi tiết |  |
| Có rollback không | Yes, No |
| Lý do rollback nếu có |  |
| Rủi ro còn lại |  |
| Action item phát sinh |  |
| Người xác nhận hoàn tất |  |

## 9. Danh mục evidence theo loại thao tác

| Loại thao tác | Evidence bắt buộc |
|---|---|
| Backup | Backup job status, backup file, size, duration, encryption status nếu có, retention location |
| Restore | Source backup, recovery point, restore log, validation query, requester confirmation |
| Failover | Role trước và sau, replication status, endpoint status, application connectivity, monitoring snapshot |
| User management | Approval, user hoặc role được cấp, scope quyền, thời hạn quyền, kiểm tra sau cấp quyền |
| Performance troubleshooting | Metric trước và sau, query, execution plan, wait, lock, action đã thực hiện, kết quả cải thiện |
| Blocking hoặc deadlock | Blocking chain, blocker, victim, query liên quan, quyết định kill nếu có, evidence sau xử lý |
| Upgrade hoặc patch | Version trước và sau, backup trước nâng cấp, compatibility check, post check, rollback evidence nếu có |

## 10. Kiểm soát chất lượng evidence

| # | Câu hỏi kiểm tra | Kết quả |
|---:|---|---|
| 1 | Người khác có thể hiểu thao tác đã làm gì không | Pass, Fail |
| 2 | Evidence có gắn với ticket hoặc change không | Pass, Fail |
| 3 | Có lộ secret hoặc dữ liệu nhạy cảm không | Pass, Fail |
| 4 | Có evidence trước và sau với thao tác production không | Pass, Fail |
| 5 | Có kết quả kiểm tra rõ ràng không | Pass, Fail |
| 6 | Có action item nếu phát hiện sai lệch không | Pass, Fail, N/A |

## 99. Lịch sử thay đổi

| Version | Ngày | Người cập nhật | Nội dung thay đổi |
|---|---|---|---|
| 0.1 | 2026-05-18 | DBA Team | Khởi tạo bản nháp |
| 0.2 | 2026-05-18 | DBA Team | Chuẩn hóa doc ID |
