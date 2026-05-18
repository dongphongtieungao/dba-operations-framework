# SOP Performance Troubleshooting

## Thông tin tài liệu

| Trường | Giá trị |
|--------|--------|
| Mã tài liệu | DBA-SOP-025 |
| Loại tài liệu | SOP |
| Mức ưu tiên triển khai | 4 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Service Owner, Infra Lead |
| Approver | DBA Lead |
| Trạng thái | Draft |
| Phiên bản | 0.2 |
| Ngày tạo | 2026-05-18 |
| Ngày review gần nhất | 2026-05-18 |
| Ngày review tiếp theo | 2026-11-18 |
| Phạm vi áp dụng | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi policy |

### Lịch sử thay đổi

| Phiên bản | Ngày | Người thay đổi | Mô tả |
|-----------|------|----------------|-------|
| 0.1 | 2026-05-18 | DBA Team | Bản draft đầu tiên |
| 0.2 | 2026-05-18 | DBA Team | Chuẩn hóa metadata, cross-reference |

## 1. Mục đích

Chuẩn hóa điều tra hiệu năng database để xác định triệu chứng, khoanh vùng nguyên nhân, đưa ra biện pháp giảm thiểu và đề xuất khắc phục gốc rễ dựa trên evidence.

## 2. Phạm vi

Áp dụng cho sự cố chậm query, chậm transaction, tăng latency, CPU cao, IO cao, memory pressure, lock, deadlock, connection saturation, replication lag và các vấn đề hiệu năng liên quan database.

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

1. Ứng dụng báo chậm hoặc timeout.
2. Monitoring cảnh báo latency, CPU, IO, connection, lock hoặc wait tăng cao.
3. Có slow query hoặc top query bất thường.
4. Sau thay đổi release, index, schema, statistic hoặc workload.
5. Có yêu cầu tuning từ application team hoặc service owner.

## 5. Điều kiện tiên quyết

1. Có khoảng thời gian xảy ra vấn đề.
2. Có thông tin ứng dụng, database, endpoint, user hoặc job liên quan.
3. Có quyền đọc metric, query plan, session, wait, lock và log.
4. Có baseline hoặc dữ liệu so sánh trước sau nếu có.
5. Có quy định rõ thao tác nào được phép làm khẩn cấp trên production.

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

### 7.1. Ghi nhận triệu chứng

1. Xác định vấn đề là chậm toàn hệ thống, chậm một chức năng, chậm một query hay chậm một job.
2. Ghi lại thời gian bắt đầu, thời gian kết thúc, phạm vi người dùng bị ảnh hưởng và lỗi cụ thể.
3. Đối chiếu với release, change hoặc incident gần nhất.

### 7.2. Kiểm tra saturation tài nguyên

1. Kiểm tra CPU, memory, disk latency, IOPS, network, temp space, log usage và connection count.
2. Xác định tài nguyên nào chạm ngưỡng hoặc thay đổi bất thường so với baseline.
3. Kiểm tra hệ điều hành hoặc cloud metric nếu có quyền.

### 7.3. Kiểm tra session, wait và lock

1. Xác định session đang chạy lâu, session blocking, wait event hoặc wait type chiếm ưu thế.
2. Tách biệt vấn đề do lock, IO, CPU, memory, network hay query plan.
3. Nếu có blocking nghiêm trọng, chuyển sang SOP Blocking and Deadlock Handling.

### 7.4. Xác định query hoặc workload gây ảnh hưởng

1. Lấy top query theo duration, CPU, logical read, physical read, execution count hoặc wait.
2. Kiểm tra query plan, cardinality estimate, index usage, missing index, scan lớn và join bất thường.
3. Kiểm tra parameter sniffing, stale statistics, plan regression hoặc implicit conversion nếu DBMS hỗ trợ phân tích.

### 7.5. Đưa ra mitigation tạm thời

1. Đề xuất kill session chỉ khi có đánh giá rủi ro và approval phù hợp.
2. Có thể giới hạn workload, tạm dừng job, tăng tài nguyên tạm thời hoặc rollback release theo tình huống.
3. Không tạo index production khẩn cấp nếu chưa đánh giá lock, dung lượng và rủi ro write overhead.

### 7.6. Phân tích nguyên nhân gốc

1. Xác định nguyên nhân thuộc query design, index, statistic, data growth, schema change, workload change, configuration, resource hoặc application behavior.
2. Thu thập evidence before after nếu có tuning.
3. Ghi rõ hành động khắc phục và hành động phòng ngừa.

### 7.7. Bàn giao kết quả

1. Cập nhật ticket với triệu chứng, nguyên nhân, evidence, mitigation và recommendation.
2. Nếu cần thay đổi production, tạo RFC riêng theo Change Management Policy.
3. Nếu là sự cố nghiêm trọng, tạo PIR hoặc RCA.

## 8. Evidence bắt buộc

1. Timeline sự cố.
2. Dashboard metric tại thời điểm lỗi.
3. Top query hoặc top wait.
4. Execution plan hoặc explain plan.
5. Lock chain hoặc session snapshot nếu có.
6. Log lỗi hoặc timeout.
7. Kết quả before after nếu đã tuning.

## 9. Rollback hoặc phương án khôi phục

Với thao tác mitigation như kill session, thay đổi parameter, tạo index hoặc update statistics, phải có kế hoạch quay lại trạng thái trước thay đổi. Nếu thay đổi cấu hình gây ảnh hưởng, revert ngay theo change ticket hoặc emergency change.

## 10. Điều kiện escalation

1. Ảnh hưởng production diện rộng.
2. Không xác định được nguyên nhân trong thời gian SLA.
3. Cần kill session quan trọng hoặc dừng job business critical.
4. Cần thay đổi cấu hình engine hoặc scale tài nguyên.
5. Có nghi ngờ lỗi ứng dụng, hạ tầng hoặc network cần phối hợp nhiều team.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Dùng Query Store, execution plan, wait stats, DMVs, blocking session và statistics. |
| PostgreSQL | Dùng pg stat activity, pg stat statements nếu có, EXPLAIN ANALYZE, wait event, vacuum và bloat check. |
| MySQL MariaDB | Dùng processlist, performance schema, slow query log, EXPLAIN và InnoDB status. |
| Oracle | Dùng AWR, ASH, execution plan, wait event, session và SQL Monitor nếu có. |
| Azure SQL | Dùng Query Store, Azure Monitor, Intelligent Insights nếu được bật và wait stats. |
| Z DB | Dùng công cụ monitoring, slow query và diagnostic interface do nền tảng Z DB cung cấp. |

## 12. Definition of Done

1. Triệu chứng và phạm vi ảnh hưởng đã được ghi nhận.
2. Nguyên nhân khả dĩ hoặc nguyên nhân gốc đã được xác định với evidence.
3. Mitigation đã được thực hiện hoặc khuyến nghị rõ ràng.
4. Nếu cần thay đổi, RFC đã được tạo.
5. Ticket đã có kết luận và action item.

## 13. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-SOP-026 | SOP Blocking and Deadlock Handling | SOP blocking liên quan |
| DBA-POL-004 | Database Change Management Policy | RFC cho tuning |
| DBA-OM-001 | DBA Service Catalog | Service mapping |
| DBA-TMP-048 | Template Operation Evidence | Template evidence |
| DBA-TMP-047 | Template Post Incident Review | Template PIR |

## 14. Checklist rút gọn

1. Ticket hoặc trigger đã rõ.
2. Phạm vi database và môi trường đã xác định.
3. Approval đã có nếu tác động production hoặc dữ liệu nhạy cảm.
4. Rủi ro đã được đánh giá.
5. Các bước kỹ thuật đã thực hiện theo SOP hoặc runbook.
6. Kết quả đã được kiểm tra.
7. Evidence đã được lưu.
8. Ticket đã được cập nhật hoặc đóng đúng trạng thái.
