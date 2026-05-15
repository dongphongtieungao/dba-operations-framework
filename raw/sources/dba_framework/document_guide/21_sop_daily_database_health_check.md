# SOP Daily Database Health Check
## Thông tin tài liệu
| Trường | Giá trị |
|---|---|
| Mã tài liệu | DBA SOP 021 |
| Loại tài liệu | SOP |
| Mức ưu tiên triển khai | 2 |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Database Lead, Service Owner, Security hoặc Infra Lead tùy phạm vi |
| Trạng thái | Draft |
| Phiên bản | 0.1 |
| Phạm vi DBMS | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi policy |

## 1. Mục đích

Chuẩn hóa kiểm tra hằng ngày đối với database để phát hiện sớm rủi ro về trạng thái instance, tài nguyên, backup, replication, lỗi hệ thống, lỗi đăng nhập, blocking và alert tồn đọng.

## 2. Phạm vi

Áp dụng cho hoạt động kiểm tra sức khỏe hằng ngày của SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các database thuộc phạm vi vận hành của DBA team.

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

1. Bắt đầu ca vận hành hằng ngày.
2. Có yêu cầu xác nhận trạng thái hệ thống trước giờ cao điểm.
3. Sau sự cố hoặc sau thay đổi lớn cần kiểm tra lại trạng thái database.
4. Trước khi bàn giao ca hoặc trước khi gửi daily report.

## 5. Điều kiện tiên quyết

1. Có danh sách database hoặc instance thuộc phạm vi quản lý.
2. Có quyền đọc monitoring, log, backup status và trạng thái replication.
3. Có dashboard hoặc script health check đã được chuẩn hóa.
4. Có mẫu Operation Evidence hoặc Daily Health Check Report.

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

### 7.1. Xác nhận phạm vi kiểm tra

1. Lấy danh sách instance, database, cluster, replica và môi trường cần kiểm tra.
2. Đối chiếu với CMDB hoặc inventory để tránh bỏ sót hệ thống.
3. Đánh dấu hệ thống đang maintenance để không đánh giá nhầm là lỗi vận hành.

### 7.2. Kiểm tra trạng thái instance

1. Xác nhận database service đang hoạt động.
2. Kiểm tra trạng thái primary, secondary, replica hoặc standby.
3. Kiểm tra thời gian uptime và sự kiện restart bất thường.
4. Kiểm tra database ở trạng thái offline, suspect, recovery, read only bất thường hoặc trạng thái tương đương theo DBMS.

### 7.3. Kiểm tra tài nguyên hệ thống

1. Kiểm tra CPU, memory, disk usage, IOPS, network và connection count.
2. Kiểm tra filesystem hoặc volume chứa data file, log file, temp file, backup file.
3. Xác định database có tốc độ tăng trưởng bất thường trong 24 giờ gần nhất.

### 7.4. Kiểm tra backup

1. Xác nhận backup gần nhất thành công theo RPO đã cam kết.
2. Kiểm tra backup job failed, backup overdue, backup size bất thường hoặc backup duration tăng đột biến.
3. Kiểm tra backup storage còn đủ dung lượng.

### 7.5. Kiểm tra replication và HA

1. Kiểm tra replication lag hoặc apply lag.
2. Kiểm tra trạng thái replica, availability group, Data Guard, streaming replication hoặc cơ chế HA tương ứng.
3. Xác nhận không có replica bị broken, paused, disconnected hoặc quá hạn đồng bộ.

### 7.6. Kiểm tra log và cảnh báo

1. Đọc error log, alert log hoặc system log trong khoảng thời gian từ lần kiểm tra trước.
2. Ghi nhận failed login, deadlock, crash recovery, timeout, disk warning, corruption warning.
3. Kiểm tra alert tồn đọng chưa xử lý hoặc chưa đóng ticket.

### 7.7. Kiểm tra blocking và session bất thường

1. Kiểm tra long running transaction, blocking chain, idle transaction và session tiêu thụ tài nguyên cao.
2. Đánh giá có cần tạo incident hoặc ticket performance riêng hay không.

### 7.8. Ghi nhận evidence và báo cáo

1. Điền kết quả vào mẫu Daily Health Check Report.
2. Gắn evidence vào ticket hoặc thư mục evidence theo ngày.
3. Tạo ticket incident nếu phát hiện lỗi có ảnh hưởng dịch vụ.

## 8. Evidence bắt buộc

1. Danh sách instance đã kiểm tra.
2. Kết quả trạng thái service, database và replica.
3. Ảnh dashboard hoặc output script health check.
4. Kết quả backup gần nhất.
5. Danh sách alert tồn đọng.
6. Nhận định rủi ro và ticket phát sinh nếu có.

## 9. Rollback hoặc phương án khôi phục

Không áp dụng rollback vì SOP này chủ yếu là read only. Nếu trong quá trình kiểm tra phát hiện lỗi và cần thao tác khắc phục, phải mở ticket riêng và áp dụng SOP tương ứng.

## 10. Điều kiện escalation

1. Backup thất bại vượt RPO.
2. Replication lag vượt ngưỡng cảnh báo.
3. Disk usage vượt ngưỡng nguy hiểm.
4. Database hoặc replica mất kết nối.
5. Có dấu hiệu hỏng dữ liệu, crash recovery hoặc lỗi bảo mật nghiêm trọng.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Kiểm tra SQL Server service, Agent job, Always On dashboard, error log, Query Store và backup history. |
| PostgreSQL | Kiểm tra pg stat activity, pg stat replication, WAL archive, autovacuum warning, disk và log. |
| MySQL MariaDB | Kiểm tra processlist, replication status, binlog, error log, slow query log và backup job. |
| Oracle | Kiểm tra alert log, listener, tablespace usage, RMAN status, Data Guard status và session. |
| Azure SQL | Kiểm tra Azure Monitor, Query Store, automatic backup, failover group, DTU hoặc vCore metric. |
| Z DB | Áp dụng theo dashboard, log, backup và HA mechanism do nền tảng Z DB cung cấp. |

## 12. Definition of Done

1. Toàn bộ database trong phạm vi đã được kiểm tra.
2. Không còn alert critical chưa được phân loại.
3. Backup và replication đã được xác nhận hoặc đã tạo ticket xử lý.
4. Evidence đã được lưu đầy đủ.
5. Daily report đã được gửi hoặc cập nhật vào hệ thống quản trị.

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
