# Backup and Restore Policy

## Thông tin tài liệu

| Trường | Giá trị |
|--------|---------|
| Mã tài liệu | DBA-POL-005 |
| Loại tài liệu | Policy |
| Mức ưu tiên triển khai | 1 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Infra Lead, Security Lead |
| Approver | Service Owner |
| Trạng thái | Draft |
| Phiên bản | 0.2 |
| Ngày tạo | 2026-05-18 |
| Ngày review gần nhất | 2026-05-18 |
| Ngày review tiếp theo | 2026-11-18 |
| Phạm vi áp dụng | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật |

### Lịch sử thay đổi

| Phiên bản | Ngày | Người thay đổi | Mô tả |
|-----------|------|----------------|-------|
| 0.1 | 2026-05-18 | DBA Team | Bản draft đầu tiên |
| 0.2 | 2026-05-18 | DBA Team | Bổ sung backup tier table, immutable backup, system DB, chuẩn hóa metadata |

## 1. Mục đích

Chính sách này định nghĩa nguyên tắc bảo vệ dữ liệu thông qua backup, restore, retention, encryption, offsite copy và restore drill. Mục tiêu là đảm bảo dữ liệu có thể được khôi phục đúng RPO và RTO đã cam kết.

## 2. Phạm vi

Áp dụng cho tất cả database production, database quan trọng tại non production, backup file, snapshot, transaction log, WAL, binlog, archive log và long term retention.

Bao gồm cả system database nếu DBMS có yêu cầu. Ví dụ: SQL Server phải backup master, msdb, model. PostgreSQL phải backup global objects.

## 3. Nguyên tắc

1. Backup thành công không đủ để chứng minh an toàn dữ liệu.
2. Restore test là bằng chứng quan trọng nhất về khả năng khôi phục.
3. Mỗi database production phải có RPO và RTO được ghi nhận.
4. Backup phải được giám sát và cảnh báo.
5. Backup chứa dữ liệu nhạy cảm phải được mã hóa.
6. Backup phải được bảo vệ khỏi xóa nhầm hoặc xóa trái phép.
7. Backup policy phải phù hợp với data classification và business criticality.
8. Restore production phải có approval và evidence.

## 4. RPO và RTO

### 4.1. RPO

RPO là mức mất dữ liệu tối đa có thể chấp nhận được tính theo thời gian. Mỗi database production phải có RPO được service owner phê duyệt.

### 4.2. RTO

RTO là thời gian tối đa để khôi phục dịch vụ hoặc dữ liệu sau sự cố. Mỗi database production phải có RTO được service owner phê duyệt.

### 4.3. Quy tắc áp dụng

1. Database business critical phải có RPO và RTO rõ ràng trước khi go live.
2. RPO và RTO phải được kiểm chứng bằng restore drill hoặc DR drill.
3. Nếu RPO hoặc RTO không thể đạt bằng kiến trúc hiện tại, DBA phải ghi nhận risk register.

## 5. Backup tier đề xuất

Bảng dưới đây là mẫu tham khảo. Giá trị cụ thể phải được service owner phê duyệt cho từng hệ thống.

| Thuộc tính | Tier 1 Critical | Tier 2 Important | Tier 3 Standard | Tier 4 Dev/Test |
|-----------|----------------|-----------------|----------------|----------------|
| RPO | ≤ 5 phút | ≤ 1 giờ | ≤ 24 giờ | ≤ 24 giờ hoặc không yêu cầu |
| RTO | ≤ 1 giờ | ≤ 4 giờ | ≤ 24 giờ | ≤ 48 giờ hoặc không yêu cầu |
| Full backup | Hằng ngày | Hằng ngày | Hằng ngày | Hằng tuần |
| Log/WAL/Binlog backup | 5–15 phút | 15–30 phút | Không bắt buộc | Không bắt buộc |
| Differential/Incremental | Mỗi 4–6 giờ | Mỗi 6–12 giờ | Không bắt buộc | Không bắt buộc |
| Retention | 30 ngày trở lên | 14 ngày trở lên | 7 ngày trở lên | 3 ngày |
| Offsite copy | Bắt buộc | Khuyến nghị | Tùy chọn | Không yêu cầu |
| Encryption | Bắt buộc | Bắt buộc nếu P2/P3 | Tùy chọn | Không yêu cầu |
| Immutable backup | Bắt buộc | Khuyến nghị | Tùy chọn | Không yêu cầu |
| Restore drill | Hằng quý | 6 tháng | Hằng năm | Không bắt buộc |

> **DBMS Implementation**: Xem chi tiết backup implementation cho từng DBMS tại: SQL Server (DBA-APP-001), PostgreSQL (DBA-APP-002), MySQL/MariaDB (DBA-APP-003), Oracle (DBA-APP-004), Azure SQL (DBA-APP-005).

## 6. Loại backup

### 6.1. Full backup

Backup toàn bộ database hoặc toàn bộ phạm vi dữ liệu theo DBMS.

### 6.2. Incremental hoặc differential backup

Backup phần thay đổi từ mốc trước đó tùy DBMS hỗ trợ.

### 6.3. Transaction log, WAL, binlog hoặc archive log backup

Backup log để hỗ trợ point in time recovery.

### 6.4. Snapshot

Snapshot có thể dùng như một lớp bảo vệ bổ sung, nhưng không thay thế hoàn toàn backup nếu chưa chứng minh được khả năng restore độc lập.

### 6.5. Export logic

Export bằng dump hoặc công cụ logic có thể dùng cho migration, kiểm thử hoặc backup bổ sung, nhưng phải đánh giá giới hạn về consistency, performance và thời gian restore.

### 6.6. System database backup

Đối với DBMS có system database riêng, phải backup theo yêu cầu. Ví dụ SQL Server phải backup master, msdb, model. Mất system database có thể dẫn đến không khôi phục được instance.

## 7. Retention backup

1. Retention phải dựa trên yêu cầu nghiệp vụ, pháp lý và data lifecycle.
2. Backup production phải có retention tối thiểu được phê duyệt.
3. Long term retention phải có owner và chi phí được chấp nhận.
4. Backup hết hạn phải được xóa theo quy trình được kiểm soát.
5. Không xóa backup trước thời hạn nếu không có approval.

## 8. Offsite backup

1. Database critical phải có bản backup hoặc bản sao khôi phục ở vị trí khác với hệ thống chính nếu yêu cầu DR.
2. Offsite backup phải được kiểm tra khả năng truy cập.
3. Offsite backup chứa dữ liệu nhạy cảm phải được mã hóa.
4. Offsite backup phải có retention rõ ràng.

## 9. Immutable backup

1. Database Tier 1 phải có ít nhất một bản backup immutable hoặc WORM nếu platform hỗ trợ.
2. Immutable backup giúp bảo vệ khỏi ransomware, xóa nhầm và insider threat.
3. Thời gian immutability phải được service owner phê duyệt.
4. Không ai được phép xóa hoặc sửa immutable backup trước khi hết hạn immutability.
5. Nếu platform không hỗ trợ immutable backup, phải có biện pháp thay thế được ghi nhận.

## 10. Encryption backup

1. Backup chứa P2 hoặc P3 phải được mã hóa.
2. Key dùng để mã hóa backup phải được bảo vệ.
3. Nếu mất key khiến backup không thể restore, phải ghi nhận sự cố nghiêm trọng.
4. Key rotation phải kiểm tra tương thích với restore backup cũ.

## 11. Backup monitoring

Phải giám sát tối thiểu các chỉ số sau:

1. Backup job status.
2. Backup age.
3. Backup duration.
4. Backup size.
5. Backup failure count.
6. Log backup delay.
7. Backup storage free space.
8. Backup chain validity nếu DBMS hỗ trợ.
9. Replication hoặc log archive status nếu ảnh hưởng PITR.

## 12. Restore drill

1. Restore drill phải được thực hiện định kỳ theo backup tier.
2. Database critical phải được ưu tiên restore drill.
3. Restore drill phải đo thời gian thực tế.
4. Restore drill phải kiểm tra dữ liệu sau restore.
5. Restore drill phải tạo báo cáo gồm RTO thực tế, RPO thực tế, lỗi phát sinh và action item.
6. Không coi một hệ thống đạt yêu cầu backup nếu restore drill liên tục thất bại.

## 13. Restore production

1. Restore production phải có ticket.
2. Restore production phải có approval của service owner.
3. Phải xác định recovery point trước khi restore.
4. Phải thông báo ảnh hưởng cho application team.
5. Phải có phương án bảo vệ dữ liệu hiện tại nếu restore có nguy cơ ghi đè.
6. Phải có validation sau restore.
7. Phải lưu evidence đầy đủ.

## 14. Backup failure handling

1. Backup failed phải tạo alert hoặc ticket.
2. DBA phải xác định nguyên nhân.
3. Nếu backup failed vượt ngưỡng RPO, phải escalation.
4. Sau khi xử lý, DBA phải chạy backup bù nếu cần.
5. Phải ghi nhận nguyên nhân và biện pháp ngăn tái diễn nếu lỗi lặp lại.

## 15. Ngoại lệ

Ngoại lệ backup chỉ được chấp nhận khi:

1. Có lý do nghiệp vụ hoặc kỹ thuật rõ ràng.
2. Có đánh giá rủi ro.
3. Có phê duyệt của service owner.
4. Có ngày hết hạn ngoại lệ.
5. Có ghi nhận trong risk register.

## 16. Evidence bắt buộc

### 16.1. Backup evidence

1. Database.
2. Backup type.
3. Thời gian backup.
4. Kết quả backup.
5. Backup size.
6. Vị trí backup.
7. Lỗi nếu có.

### 16.2. Restore evidence

1. Ticket ID.
2. Database nguồn.
3. Database đích.
4. Recovery point.
5. Backup source.
6. Người thực hiện.
7. Thời gian bắt đầu và kết thúc.
8. Restore log.
9. Validation result.
10. Người xác nhận.

## 17. Chỉ số tuân thủ

1. Backup success rate.
2. Backup overdue count.
3. Restore drill success rate.
4. RPO compliance rate.
5. RTO compliance rate.
6. Backup storage risk count.
7. Backup encryption compliance.
8. Số backup failure lặp lại.
9. Immutable backup coverage cho Tier 1.

## 18. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-POL-001 | Data Lifecycle Management Policy | Retention và data classification |
| DBA-POL-002 | Database Security Policy | Encryption và key management |
| DBA-POL-007 | Database HA and DR Policy | RPO, RTO và DR strategy |
| DBA-POL-006 | Database Audit and Compliance Policy | Audit backup restore event |
| DBA-OM-003 | DBA RACI Matrix | Trách nhiệm backup restore |
