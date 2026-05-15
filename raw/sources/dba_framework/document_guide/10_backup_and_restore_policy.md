# Backup and Restore Policy

## Thông tin tài liệu

1. Mã tài liệu: DBA POL 005
2. Loại tài liệu: Policy
3. Mức ưu tiên triển khai: 1
4. Owner đề xuất: DBA Team
5. Trạng thái: Draft
6. Phiên bản: 0.1
7. Phạm vi áp dụng: SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid
8. Chu kỳ review: 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật

## 1. Mục đích

Chính sách này định nghĩa nguyên tắc bảo vệ dữ liệu thông qua backup, restore, retention, encryption, offsite copy và restore drill. Mục tiêu là đảm bảo dữ liệu có thể được khôi phục đúng RPO và RTO đã cam kết.

## 2. Phạm vi

Áp dụng cho tất cả database production, database quan trọng tại non production, backup file, snapshot, transaction log, WAL, binlog, archive log và long term retention.

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

## 5. Loại backup

### 5.1. Full backup

Backup toàn bộ database hoặc toàn bộ phạm vi dữ liệu theo DBMS.

### 5.2. Incremental hoặc differential backup

Backup phần thay đổi từ mốc trước đó tùy DBMS hỗ trợ.

### 5.3. Transaction log, WAL, binlog hoặc archive log backup

Backup log để hỗ trợ point in time recovery.

### 5.4. Snapshot

Snapshot có thể dùng như một lớp bảo vệ bổ sung, nhưng không thay thế hoàn toàn backup nếu chưa chứng minh được khả năng restore độc lập.

### 5.5. Export logic

Export bằng dump hoặc công cụ logic có thể dùng cho migration, kiểm thử hoặc backup bổ sung, nhưng phải đánh giá giới hạn về consistency, performance và thời gian restore.

## 6. Retention backup

1. Retention phải dựa trên yêu cầu nghiệp vụ, pháp lý và data lifecycle.
2. Backup production phải có retention tối thiểu được phê duyệt.
3. Long term retention phải có owner và chi phí được chấp nhận.
4. Backup hết hạn phải được xóa theo quy trình được kiểm soát.
5. Không xóa backup trước thời hạn nếu không có approval.

## 7. Offsite backup

1. Database critical phải có bản backup hoặc bản sao khôi phục ở vị trí khác với hệ thống chính nếu yêu cầu DR.
2. Offsite backup phải được kiểm tra khả năng truy cập.
3. Offsite backup chứa dữ liệu nhạy cảm phải được mã hóa.
4. Offsite backup phải có retention rõ ràng.

## 8. Encryption backup

1. Backup chứa P2 hoặc P3 phải được mã hóa.
2. Key dùng để mã hóa backup phải được bảo vệ.
3. Nếu mất key khiến backup không thể restore, phải ghi nhận sự cố nghiêm trọng.
4. Key rotation phải kiểm tra tương thích với restore backup cũ.

## 9. Backup monitoring

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

## 10. Restore drill

1. Restore drill phải được thực hiện định kỳ với database đại diện.
2. Database critical phải được ưu tiên restore drill.
3. Restore drill phải đo thời gian thực tế.
4. Restore drill phải kiểm tra dữ liệu sau restore.
5. Restore drill phải tạo báo cáo gồm RTO thực tế, RPO thực tế, lỗi phát sinh và action item.
6. Không coi một hệ thống đạt yêu cầu backup nếu restore drill liên tục thất bại.

## 11. Restore production

1. Restore production phải có ticket.
2. Restore production phải có approval của service owner.
3. Phải xác định recovery point trước khi restore.
4. Phải thông báo ảnh hưởng cho application team.
5. Phải có phương án bảo vệ dữ liệu hiện tại nếu restore có nguy cơ ghi đè.
6. Phải có validation sau restore.
7. Phải lưu evidence đầy đủ.

## 12. Backup failure handling

1. Backup failed phải tạo alert hoặc ticket.
2. DBA phải xác định nguyên nhân.
3. Nếu backup failed vượt ngưỡng RPO, phải escalation.
4. Sau khi xử lý, DBA phải chạy backup bù nếu cần.
5. Phải ghi nhận nguyên nhân và biện pháp ngăn tái diễn nếu lỗi lặp lại.

## 13. Evidence bắt buộc

### 13.1. Backup evidence

1. Database.
2. Backup type.
3. Thời gian backup.
4. Kết quả backup.
5. Backup size.
6. Vị trí backup.
7. Lỗi nếu có.

### 13.2. Restore evidence

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

## 14. Chỉ số tuân thủ

1. Backup success rate.
2. Backup overdue count.
3. Restore drill success rate.
4. RPO compliance rate.
5. RTO compliance rate.
6. Backup storage risk count.
7. Backup encryption compliance.
8. Số backup failure lặp lại.
