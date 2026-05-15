# SOP Restore Operation
## Thông tin tài liệu
| Trường | Giá trị |
|---|---|
| Mã tài liệu | DBA SOP 024 |
| Loại tài liệu | SOP |
| Mức ưu tiên triển khai | 1 |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Database Lead, Service Owner, Security hoặc Infra Lead tùy phạm vi |
| Trạng thái | Draft |
| Phiên bản | 0.1 |
| Phạm vi DBMS | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi policy |

## 1. Mục đích

Chuẩn hóa phục hồi dữ liệu để đảm bảo restore đúng database, đúng thời điểm, đúng môi trường, đúng mục đích và có xác nhận kết quả rõ ràng.

## 2. Phạm vi

Áp dụng cho restore full, incremental, differential, transaction log, WAL, binlog, point in time recovery, restore sang test, restore production và restore phục vụ DR.

## 3. Nguyên tắc áp dụng

Bộ SOP này thuộc DBA Operations Framework. Mỗi SOP được thiết kế để dùng trong vận hành thực tế, có thể gắn với ticket ITSM, ServiceNow, Jira hoặc công cụ quản trị nội bộ. Khi áp dụng cho từng DBMS cụ thể, DBA cần dùng thêm phụ lục kỹ thuật tương ứng như SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle hoặc Z DB.


| Thành phần | Nội dung |
|---|---|
| Ticket bắt buộc | Có |
| Approval bắt buộc | Tùy mức rủi ro và môi trường |
| Evidence bắt buộc | Có |
| Áp dụng production | Có, nhưng phải tuân thủ Change Management |
| Áp dụng non production | Có, có thể rút gọn approval theo quy định nội bộ |
| Tài liệu liên quan | DBA Service Catalog, DBA Operating Model, DBA RACI Matrix, DBA Document Governance Standard |

## 4. Khi nào sử dụng SOP này

1. Requester yêu cầu restore dữ liệu.
2. Cần phục hồi sau lỗi thao tác hoặc lỗi ứng dụng.
3. Cần khôi phục sau hỏng dữ liệu.
4. Cần restore để kiểm thử backup hoặc diễn tập DR.
5. Cần tạo môi trường test từ bản backup đã được phép sử dụng.

## 5. Điều kiện tiên quyết

1. Có ticket hoặc change request hợp lệ.
2. Có approval của service owner nếu restore ảnh hưởng production hoặc dữ liệu nhạy cảm.
3. Có recovery point hoặc thời điểm cần khôi phục rõ ràng.
4. Có xác nhận source database, target database và phạm vi dữ liệu.
5. Có đủ dung lượng và quyền truy cập backup.
6. Có kế hoạch validation sau restore.

## 6. Vai trò và trách nhiệm

| Vai trò | Trách nhiệm |
|---|---|
| DBA | Thực hiện kiểm tra, thao tác kỹ thuật, ghi nhận evidence và cập nhật ticket. |
| Database Lead | Review quyết định kỹ thuật có rủi ro cao và hỗ trợ escalation. |
| Service Owner | Phê duyệt tác động nghiệp vụ, downtime, mất dữ liệu chấp nhận được hoặc kết quả bàn giao. |
| Application Team | Xác nhận ảnh hưởng ứng dụng, hỗ trợ validation và xử lý dependency phía ứng dụng. |
| Infra hoặc Cloud Team | Hỗ trợ hạ tầng, network, storage, compute, load balancer, DNS hoặc resource cloud nếu cần. |
| Security Team | Review yêu cầu liên quan quyền cao, dữ liệu nhạy cảm, audit, mã hóa hoặc compliance. |

## 7. Quy trình thực hiện chi tiết

### 7.1. Xác nhận phạm vi restore

1. Xác định source database, target database, môi trường và mục đích restore.
2. Xác định restore overwrite database hiện có hay restore sang database mới.
3. Xác định recovery point theo thời gian hoặc backup ID cụ thể.
4. Xác định dữ liệu có cần masking sau restore không.

### 7.2. Kiểm tra approval và rủi ro

1. Với production restore, yêu cầu change approval hoặc emergency change approval.
2. Xác định nguy cơ mất dữ liệu sau recovery point.
3. Xác định downtime và ảnh hưởng ứng dụng.
4. Thông báo các bên liên quan trước khi thực hiện nếu có ảnh hưởng dịch vụ.

### 7.3. Chuẩn bị restore

1. Kiểm tra backup chain đầy đủ.
2. Kiểm tra backup file có thể đọc và verify.
3. Kiểm tra dung lượng target storage.
4. Backup trạng thái hiện tại của target nếu restore overwrite.
5. Dừng job hoặc ứng dụng liên quan nếu cần đảm bảo consistency.

### 7.4. Thực hiện restore

1. Chạy runbook hoặc script restore chuẩn hóa.
2. Theo dõi tiến trình restore và log lỗi.
3. Ghi nhận thời gian bắt đầu, thời gian kết thúc và duration.
4. Không tự ý thay đổi recovery point khi chưa xác nhận lại.

### 7.5. Validation sau restore

1. Kiểm tra database online và trạng thái nhất quán.
2. Kiểm tra object count, row count, checksum hoặc validation query nếu có.
3. Kiểm tra quyền truy cập, owner, job, synonym, sequence, linked object hoặc dependency theo DBMS.
4. Yêu cầu requester hoặc application owner xác nhận dữ liệu và chức năng.

### 7.6. Hoàn tất và bàn giao

1. Cập nhật ticket với kết quả restore.
2. Gắn log, output và validation evidence.
3. Ghi nhận RTO thực tế và RPO thực tế nếu là restore drill hoặc incident.
4. Cập nhật action item nếu phát hiện backup chain hoặc quy trình có vấn đề.

## 8. Evidence bắt buộc

1. Ticket hoặc change request.
2. Approval.
3. Source database, target database và recovery point.
4. Backup ID hoặc backup file đã sử dụng.
5. Restore log.
6. Validation query và kết quả.
7. Xác nhận của requester hoặc service owner.
8. RTO thực tế và RPO thực tế nếu áp dụng.

## 9. Rollback hoặc phương án khôi phục

Nếu restore sai target hoặc sai recovery point, dừng bàn giao ngay, cô lập target, xác định backup trạng thái trước restore và thực hiện restore lại theo recovery point đúng. Nếu ảnh hưởng production, kích hoạt incident hoặc emergency change.

## 10. Điều kiện escalation

1. Backup chain thiếu hoặc không đọc được.
2. Restore production có nguy cơ mất dữ liệu.
3. Restore vượt RTO cam kết.
4. Validation sau restore thất bại.
5. Requester không xác định được recovery point rõ ràng.
6. Dữ liệu nhạy cảm bị restore sang môi trường không được phép.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Kiểm tra full, differential, log restore, recovery mode, tail log backup và logical file mapping. |
| PostgreSQL | Kiểm tra base backup, WAL, recovery target time, timeline và pg wal replay. |
| MySQL MariaDB | Kiểm tra backup physical hoặc logical, binlog position, GTID và consistency sau restore. |
| Oracle | Kiểm tra RMAN restore, recover database, archive log và open resetlogs nếu áp dụng. |
| Azure SQL | Dùng point in time restore, geo restore hoặc long term retention theo khả năng dịch vụ. |
| Z DB | Thực hiện theo restore mechanism và giới hạn vận hành của nền tảng Z DB. |

## 12. Definition of Done

1. Restore hoàn tất đúng source, target và recovery point.
2. Database online và validation đạt yêu cầu.
3. Requester hoặc service owner đã xác nhận.
4. Evidence đã được lưu đầy đủ.
5. Ticket đã được cập nhật kết quả và action item nếu có.

## 13. Liên kết tài liệu liên quan

1. DBA Operations Framework Overview
2. DBA Service Catalog
3. DBA Operating Model
4. DBA RACI Matrix
5. DBA Document Governance Standard
6. Database Change Management Policy
7. Database Security Policy
8. Database Audit and Compliance Policy
9. Template Operation Evidence
10. Template Post Incident Review nếu có incident nghiêm trọng

## 14. Checklist rút gọn

1. Ticket hoặc trigger đã rõ.
2. Phạm vi database và môi trường đã xác định.
3. Approval đã có nếu tác động production hoặc dữ liệu nhạy cảm.
4. Rủi ro đã được đánh giá.
5. Các bước kỹ thuật đã thực hiện theo SOP hoặc runbook.
6. Kết quả đã được kiểm tra.
7. Evidence đã được lưu.
8. Ticket đã được cập nhật hoặc đóng đúng trạng thái.
