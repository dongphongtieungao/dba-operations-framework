# SOP Replication Troubleshooting
## Thông tin tài liệu
| Trường | Giá trị |
|---|---|
| Mã tài liệu | DBA SOP 027 |
| Loại tài liệu | SOP |
| Mức ưu tiên triển khai | 2 |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Database Lead, Service Owner, Security hoặc Infra Lead tùy phạm vi |
| Trạng thái | Draft |
| Phiên bản | 0.1 |
| Phạm vi DBMS | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi policy |

## 1. Mục đích

Chuẩn hóa xử lý lỗi replication để bảo vệ khả năng HA, DR, read scaling, backup offload và đảm bảo replica có thể sử dụng khi cần failover hoặc restore.

## 2. Phạm vi

Áp dụng cho replication lag, replication stopped, broken replication, replica disconnected, WAL hoặc binlog backlog, Always On issue, Data Guard issue, streaming replication issue và cơ chế replica tương đương.

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

1. Monitoring cảnh báo replication lag.
2. Replica bị disconnected, stopped hoặc unhealthy.
3. Failover readiness check thất bại.
4. Backup hoặc reporting phụ thuộc replica bị lỗi.
5. Daily health check phát hiện trạng thái replication bất thường.

## 5. Điều kiện tiên quyết

1. Có topology replication hoặc HA diagram.
2. Có thông tin primary, replica, replication mode, network path và lag threshold.
3. Có quyền đọc trạng thái replication trên primary và replica.
4. Có runbook hoặc hướng dẫn riêng cho DBMS liên quan.
5. Có quy định khi nào được rebuild replica hoặc promote replica.

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

### 7.1. Xác định topology và mức ảnh hưởng

1. Xác định replica nào bị lỗi, vai trò của replica và dịch vụ phụ thuộc.
2. Xác định replication là synchronous, asynchronous, logical hay physical.
3. Đánh giá ảnh hưởng tới HA, DR, backup, reporting hoặc read traffic.

### 7.2. Kiểm tra trạng thái primary

1. Kiểm tra primary online và nhận write bình thường.
2. Kiểm tra log generation rate, transaction volume, disk, CPU, network và error log.
3. Kiểm tra có thay đổi lớn, bulk load hoặc long transaction làm tăng lag không.

### 7.3. Kiểm tra trạng thái replica

1. Kiểm tra replica service, apply process, receive process, disk, network và error log.
2. Xác định replica đang lag, stopped, disconnected, read only lỗi hay corrupt.
3. Kiểm tra replication slot, binlog position, WAL position, redo apply hoặc queue tương ứng.

### 7.4. Khoanh vùng nguyên nhân

1. Phân loại nguyên nhân do network, disk, CPU, log backlog, cấu hình, permission, schema conflict, version mismatch hoặc lỗi dữ liệu.
2. Kiểm tra alert hạ tầng cùng thời điểm.
3. Kiểm tra có maintenance, restart hoặc failover trước đó không.

### 7.5. Thực hiện mitigation

1. Restart replication process nếu rủi ro thấp và được phép.
2. Tạm dừng workload phụ thuộc replica nếu replica không đáng tin cậy.
3. Tăng tài nguyên hoặc dọn dung lượng nếu bottleneck rõ ràng.
4. Nếu replica không thể bắt kịp hoặc chain hỏng, đề xuất rebuild replica theo change riêng.

### 7.6. Xác nhận khôi phục

1. Theo dõi lag giảm về ngưỡng cho phép.
2. Kiểm tra trạng thái replica healthy.
3. Kiểm tra read query hoặc backup trên replica nếu có phụ thuộc.
4. Cập nhật failover readiness nếu replica thuộc HA.

### 7.7. Ghi nhận và phòng ngừa

1. Lưu topology, lag chart, log lỗi và lệnh đã chạy.
2. Ghi nhận nguyên nhân và hành động phòng ngừa.
3. Nếu replication lag tái diễn, mở problem ticket hoặc capacity ticket.

## 8. Evidence bắt buộc

1. Replication topology.
2. Lag chart trước và sau xử lý.
3. Primary và replica status.
4. Error log liên quan.
5. Lệnh hoặc runbook đã chạy.
6. Kết quả xác nhận replica healthy.
7. Recommendation phòng ngừa.

## 9. Rollback hoặc phương án khôi phục

Nếu thao tác restart replication hoặc thay đổi cấu hình làm tình trạng xấu hơn, revert cấu hình cũ, dừng phụ thuộc read traffic nếu cần, và chuyển sang phương án rebuild replica hoặc failover theo SOP liên quan.

## 10. Điều kiện escalation

1. Replica thuộc HA không còn đủ điều kiện failover.
2. Replication lag vượt RPO hoặc vượt ngưỡng DR.
3. Replica có dấu hiệu hỏng dữ liệu.
4. Primary chịu áp lực do replica hoặc replication slot giữ log quá lâu.
5. Cần rebuild replica hoặc promote replica.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Kiểm tra Always On dashboard, replica state, synchronization state, send queue và redo queue. |
| PostgreSQL | Kiểm tra pg stat replication, replication slot, WAL retention, replay lag và pg rewind khi cần. |
| MySQL MariaDB | Kiểm tra replica IO thread, SQL thread, Seconds Behind Master hoặc Source, GTID và relay log. |
| Oracle | Kiểm tra Data Guard transport lag, apply lag, broker status và archive log. |
| Azure SQL | Kiểm tra failover group, geo replication, replication role và Azure Monitor. |
| Z DB | Theo dõi replication hoặc HA mechanism do nền tảng Z DB cung cấp. |

## 12. Definition of Done

1. Replication status đã trở về healthy hoặc có quyết định xử lý tiếp theo.
2. Lag nằm trong ngưỡng chấp nhận hoặc đã có ticket rủi ro.
3. Ảnh hưởng tới HA, DR, backup hoặc read traffic đã được đánh giá.
4. Evidence đã được lưu.
5. Action item phòng ngừa đã được tạo nếu cần.

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
