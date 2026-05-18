# SOP HA Failover and Switchover

## Thông tin tài liệu

| Trường | Giá trị |
|--------|--------|
| Mã tài liệu | DBA-SOP-028 |
| Loại tài liệu | SOP |
| Mức ưu tiên triển khai | 1 |
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

Chuẩn hóa failover và switchover để đảm bảo chuyển đổi vai trò primary, secondary hoặc standby an toàn, có kiểm soát, giảm downtime và bảo vệ dữ liệu.

## 2. Phạm vi

Áp dụng cho planned switchover, unplanned failover, HA failover, DR failover, kiểm tra sau failover và rollback hoặc failback theo chính sách HA DR.

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

1. Primary gặp sự cố nghiêm trọng.
2. Cần bảo trì primary và chuyển workload sang secondary.
3. DR drill hoặc HA drill.
4. Cần chuyển vùng hoặc chuyển site theo kế hoạch.
5. Monitoring xác nhận primary không còn đáp ứng tiêu chí dịch vụ.

## 5. Điều kiện tiên quyết

1. Có HA and DR Policy đã được phê duyệt.
2. Có topology HA và danh sách node, endpoint, DNS, connection string.
3. Có RTO, RPO và tiêu chí mất dữ liệu được chấp nhận.
4. Có approval hoặc emergency approval.
5. Có phương án truyền thông tới app, infra, network, security và service owner.
6. Có checklist pre failover và post failover.

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

### 7.1. Phân loại tình huống

1. Xác định đây là planned switchover, emergency failover hay DR failover.
2. Xác định mức ảnh hưởng, database liên quan và dịch vụ phụ thuộc.
3. Xác định tiêu chí quyết định failover đã đạt hay chưa.

### 7.2. Kiểm tra điều kiện trước chuyển đổi

1. Kiểm tra trạng thái primary và secondary.
2. Kiểm tra replication lag, data synchronization và khả năng mất dữ liệu.
3. Kiểm tra backup gần nhất.
4. Kiểm tra ứng dụng, job, batch và connection routing.
5. Thông báo maintenance hoặc incident theo quy trình.

### 7.3. Dừng hoặc kiểm soát write traffic nếu cần

1. Với switchover có kế hoạch, yêu cầu application team dừng write hoặc chuyển sang maintenance mode nếu cần.
2. Với emergency failover, đánh giá khả năng split brain trước khi promote secondary.
3. Đảm bảo không có hai primary cùng nhận write.

### 7.4. Thực hiện failover hoặc switchover

1. Chạy runbook failover đã chuẩn hóa cho DBMS tương ứng.
2. Promote secondary hoặc chuyển vai trò primary theo cơ chế HA.
3. Theo dõi log trong suốt quá trình chuyển đổi.
4. Ghi nhận thời gian bắt đầu, thời gian hoàn tất và lỗi phát sinh.

### 7.5. Kiểm tra sau failover

1. Xác nhận primary mới online và nhận write.
2. Xác nhận replica hoặc standby còn lại ở trạng thái phù hợp.
3. Kiểm tra endpoint, DNS, listener hoặc connection string.
4. Kiểm tra application connection, login, job, batch và monitoring.
5. Kiểm tra dữ liệu bằng validation query hoặc business smoke test.

### 7.6. Quyết định rollback hoặc failback

1. Nếu failover thất bại, đánh giá rollback theo kế hoạch đã phê duyệt.
2. Nếu failover thành công, lên kế hoạch rebuild hoặc resync node cũ.
3. Không failback ngay nếu chưa có đánh giá ổn định và approval.

### 7.7. Hoàn tất truyền thông và evidence

1. Thông báo kết quả cho stakeholder.
2. Cập nhật ticket, CMDB, topology, monitoring và tài liệu vận hành nếu có thay đổi lâu dài.
3. Lưu log, timeline và kết quả kiểm tra.

## 8. Evidence bắt buộc

1. Approval hoặc emergency approval.
2. Topology trước và sau failover.
3. Replication lag hoặc synchronization status trước failover.
4. Timeline chuyển đổi.
5. Log failover.
6. Kết quả kiểm tra primary mới, endpoint và ứng dụng.
7. RTO thực tế và RPO thực tế.
8. Thông báo stakeholder.

## 9. Rollback hoặc phương án khôi phục

Rollback phải dựa trên trạng thái dữ liệu và nguy cơ split brain. Nếu primary cũ có dữ liệu chưa đồng bộ, không được đưa primary cũ trở lại nhận write khi chưa reconciliation. Failback phải được xử lý như một change riêng nếu không nằm trong kế hoạch ban đầu.

## 10. Điều kiện escalation

1. Không xác định được replica đủ điều kiện promote.
2. Nguy cơ mất dữ liệu vượt RPO.
3. Có dấu hiệu split brain.
4. Application không kết nối được sau failover.
5. Failover vượt RTO.
6. Cần quyết định business về mất dữ liệu hoặc downtime.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Dùng Always On failover, listener, synchronization state, quorum và cluster status. |
| PostgreSQL | Dùng promote replica, kiểm tra timeline, pg rewind hoặc rebuild replica sau failover. |
| MySQL MariaDB | Dùng replica promotion, read only mode, GTID consistency và cập nhật routing. |
| Oracle | Dùng Data Guard switchover hoặc failover, broker nếu có và kiểm tra apply lag. |
| Azure SQL | Dùng failover group hoặc geo restore tùy kiến trúc. Kiểm tra connection endpoint và Azure Monitor. |
| Z DB | Thực hiện theo HA mechanism và quy trình phối hợp với đội nền tảng Z DB. |

## 12. Definition of Done

1. Primary mới đã hoạt động ổn định.
2. Application đã kết nối thành công.
3. Không có split brain.
4. RTO và RPO thực tế đã được ghi nhận.
5. Stakeholder đã được thông báo.
6. Evidence đã lưu đầy đủ và action item sau failover đã được tạo.

## 13. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-POL-007 | Database HA and DR Policy | Policy gốc |
| DBA-SOP-029 | SOP Disaster Recovery Drill | SOP DR drill |
| DBA-OM-001 | DBA Service Catalog | Service mapping |
| DBA-OM-003 | DBA RACI Matrix | Trách nhiệm |
| DBA-RBK-041 | Runbook Failover Execution | Runbook failover |
| DBA-TMP-048 | Template Operation Evidence | Template evidence |
| DBA-TMP-050 | Template Failover Drill Report | Template báo cáo drill |

## 14. Checklist rút gọn

1. Ticket hoặc trigger đã rõ.
2. Phạm vi database và môi trường đã xác định.
3. Approval đã có nếu tác động production hoặc dữ liệu nhạy cảm.
4. Rủi ro đã được đánh giá.
5. Các bước kỹ thuật đã thực hiện theo SOP hoặc runbook.
6. Kết quả đã được kiểm tra.
7. Evidence đã được lưu.
8. Ticket đã được cập nhật hoặc đóng đúng trạng thái.
