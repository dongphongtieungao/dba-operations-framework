# SOP Blocking and Deadlock Handling

## Thông tin tài liệu

| Trường | Giá trị |
|--------|--------|
| Mã tài liệu | DBA-SOP-026 |
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

Chuẩn hóa xử lý blocking và deadlock để giảm thời gian gián đoạn, tránh thao tác kill session cảm tính và tạo được evidence phục vụ RCA hoặc tuning.

## 2. Phạm vi

Áp dụng khi database xuất hiện blocking chain, deadlock, long running transaction, lock timeout, transaction treo hoặc ứng dụng chậm do lock contention.

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

1. Monitoring cảnh báo blocking hoặc deadlock.
2. Ứng dụng báo timeout hoặc transaction treo.
3. Daily health check phát hiện long running transaction.
4. DBA hoặc app team phát hiện lock contention khi điều tra hiệu năng.

## 5. Điều kiện tiên quyết

1. Có quyền đọc session, lock, transaction và query text.
2. Có ngưỡng xác định blocking nghiêm trọng.
3. Có quy định rõ ai được phép kill session production.
4. Có kênh liên hệ application owner hoặc service owner.

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

### 7.1. Xác định loại vấn đề

1. Phân biệt blocking, deadlock, lock timeout và long running transaction.
2. Ghi nhận thời điểm phát hiện, database, user, host, application và query liên quan.
3. Kiểm tra mức độ ảnh hưởng tới người dùng hoặc batch job.

### 7.2. Xác định blocker và victim

1. Truy vết blocking chain từ session bị chờ đến root blocker.
2. Xác định root blocker đang chạy query gì, đã chạy bao lâu và đang giữ lock gì.
3. Với deadlock, lấy deadlock graph hoặc log tương đương nếu DBMS hỗ trợ.

### 7.3. Đánh giá rủi ro trước khi can thiệp

1. Xác định root blocker thuộc ứng dụng, batch, maintenance job hay thao tác thủ công.
2. Đánh giá kill session có thể rollback lâu, mất transaction đang xử lý hoặc ảnh hưởng business không.
3. Liên hệ application owner nếu session thuộc nghiệp vụ quan trọng.

### 7.4. Thực hiện mitigation

1. Nếu blocking nhẹ và sắp kết thúc, tiếp tục theo dõi.
2. Nếu blocking nghiêm trọng và được phê duyệt, kill root blocker hoặc dừng job gây lỗi.
3. Nếu deadlock lặp lại, ghi nhận query pair, index, isolation level và transaction pattern để phân tích sau.

### 7.5. Thu thập evidence

1. Lưu session snapshot, lock chain, query text, execution plan nếu có, deadlock graph và timeline.
2. Ghi rõ session nào bị kill, ai approve, lý do và kết quả sau khi kill.
3. Lưu ảnh dashboard trước và sau mitigation.

### 7.6. Phòng ngừa tái diễn

1. Đề xuất tối ưu transaction ngắn hơn.
2. Đề xuất index phù hợp để giảm lock scan.
3. Đề xuất điều chỉnh thứ tự truy cập bảng, isolation level hoặc retry logic trong ứng dụng.
4. Tạo RCA hoặc tuning ticket nếu deadlock hoặc blocking tái diễn.

## 8. Evidence bắt buộc

1. Blocking chain hoặc lock tree.
2. Root blocker session ID và query.
3. Victim session hoặc danh sách session bị ảnh hưởng.
4. Deadlock graph nếu có.
5. Approval kill session nếu có.
6. Kết quả sau mitigation.
7. Recommendation phòng ngừa.

## 9. Rollback hoặc phương án khôi phục

Không thể rollback trực tiếp thao tác kill session ngoài việc để database tự rollback transaction bị kill. Nếu kill nhầm hoặc ảnh hưởng nghiệp vụ, kích hoạt incident, thông báo service owner và phối hợp khôi phục trạng thái ứng dụng hoặc dữ liệu theo quy trình.

## 10. Điều kiện escalation

1. Blocking ảnh hưởng production diện rộng.
2. Root blocker là transaction business critical.
3. Rollback sau kill kéo dài bất thường.
4. Deadlock lặp lại nhiều lần trong thời gian ngắn.
5. Có nghi ngờ lỗi thiết kế transaction hoặc release mới gây lock contention.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Dùng DMVs, blocking session id, deadlock graph, Extended Events và Query Store. |
| PostgreSQL | Dùng pg locks, pg stat activity, wait event, blocking pids và deadlock log. |
| MySQL MariaDB | Dùng information schema innodb locks, processlist, InnoDB status và deadlock section. |
| Oracle | Dùng v session, v lock, blocking session, ASH và trace nếu cần. |
| Azure SQL | Dùng DMVs, Query Store, blocking reports và Azure Monitor. |
| Z DB | Dùng công cụ session, lock và diagnostic do nền tảng Z DB cung cấp. |

## 12. Definition of Done

1. Blocking hoặc deadlock đã được giảm thiểu hoặc phân loại rõ.
2. Root cause khả dĩ đã được ghi nhận.
3. Evidence đầy đủ để phân tích tái diễn.
4. Có recommendation cho app, DBA hoặc infra nếu cần.
5. Ticket đã cập nhật kết quả xử lý.

## 13. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-SOP-025 | SOP Performance Troubleshooting | SOP performance liên quan |
| DBA-POL-004 | Database Change Management Policy | RFC cho mitigation |
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
