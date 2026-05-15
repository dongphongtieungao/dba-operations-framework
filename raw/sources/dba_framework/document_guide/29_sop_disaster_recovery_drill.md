# SOP Disaster Recovery Drill
## Thông tin tài liệu
| Trường | Giá trị |
|---|---|
| Mã tài liệu | DBA SOP 029 |
| Loại tài liệu | SOP |
| Mức ưu tiên triển khai | 1 |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Database Lead, Service Owner, Security hoặc Infra Lead tùy phạm vi |
| Trạng thái | Draft |
| Phiên bản | 0.1 |
| Phạm vi DBMS | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi policy |

## 1. Mục đích

Chuẩn hóa diễn tập khôi phục thảm họa để kiểm chứng khả năng phục hồi dữ liệu, khả năng chuyển đổi dịch vụ, RTO, RPO và năng lực phối hợp giữa DBA, app, infra, security và service owner.

## 2. Phạm vi

Áp dụng cho DR drill cấp database, cấp ứng dụng, cấp site, cấp cloud region hoặc môi trường hybrid on premises và cloud.

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

1. Lịch DR drill định kỳ.
2. Yêu cầu kiểm toán hoặc compliance.
3. Sau thay đổi kiến trúc HA DR.
4. Sau sự cố nghiêm trọng liên quan backup, restore, replication hoặc failover.
5. Trước khi nghiệm thu hệ thống business critical.

## 5. Điều kiện tiên quyết

1. Có HA and DR Policy và Backup and Restore Policy.
2. Có kịch bản DR được phê duyệt.
3. Có danh sách database, ứng dụng, dependency, RTO, RPO và owner.
4. Có môi trường hoặc site DR đã sẵn sàng.
5. Có kế hoạch truyền thông, nhân sự tham gia và tiêu chí nghiệm thu.
6. Có phương án tránh ảnh hưởng production nếu chỉ là drill.

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

### 7.1. Xác định phạm vi drill

1. Chọn database, application, service hoặc site cần diễn tập.
2. Xác định loại drill là tabletop, restore drill, failover drill, full DR drill hoặc hybrid DR drill.
3. Xác định tiêu chí thành công, RTO mục tiêu, RPO mục tiêu và phạm vi validation.

### 7.2. Chuẩn bị trước drill

1. Kiểm tra backup, replication, DR site, network, credential, DNS, firewall và monitoring.
2. Xác nhận vai trò của DBA, app, infra, security, service owner và observer.
3. Chuẩn bị timeline, checklist và template DR Drill Report.
4. Thông báo lịch drill và freeze thay đổi nếu cần.

### 7.3. Thực hiện kịch bản DR

1. Mô phỏng mất primary database, mất site hoặc mất region theo kịch bản.
2. Thực hiện restore hoặc failover theo SOP tương ứng.
3. Ghi lại thời gian từng bước, lỗi phát sinh và quyết định quan trọng.
4. Không bỏ qua bước validation để rút ngắn thời gian báo cáo.

### 7.4. Validation dịch vụ

1. Kiểm tra database online, dữ liệu đúng recovery point và ứng dụng kết nối được.
2. Chạy smoke test nghiệp vụ đã được app team xác nhận trước.
3. Kiểm tra job, batch, integration, monitoring và alert sau khi chuyển đổi.
4. Đo RTO thực tế và RPO thực tế.

### 7.5. Khôi phục trạng thái sau drill

1. Nếu drill không chuyển production thật, xóa hoặc cô lập dữ liệu test theo policy.
2. Nếu có chuyển đổi thật, lên kế hoạch failback hoặc giữ trạng thái mới theo quyết định service owner.
3. Đảm bảo monitoring, alert và topology được cập nhật.

### 7.6. Tổng kết và cải tiến

1. Tạo DR Drill Report.
2. Ghi nhận gap giữa mục tiêu và thực tế.
3. Tạo action item với owner và deadline.
4. Cập nhật SOP, runbook, policy hoặc architecture nếu phát hiện điểm yếu.

## 8. Evidence bắt buộc

1. Kịch bản DR đã phê duyệt.
2. Danh sách hệ thống tham gia.
3. Timeline từng bước.
4. Backup, restore hoặc failover log.
5. Kết quả validation database và application.
6. RTO thực tế và RPO thực tế.
7. Danh sách lỗi, gap và action item.

## 9. Rollback hoặc phương án khôi phục

Rollback phụ thuộc loại drill. Nếu drill trên môi trường cô lập, xóa dữ liệu và resource test theo policy. Nếu drill có chuyển đổi thật, failback phải được xử lý như một change riêng với validation đầy đủ.

## 10. Điều kiện escalation

1. Không đạt RTO hoặc RPO mục tiêu.
2. Backup không restore được.
3. DR site không sẵn sàng.
4. Ứng dụng không chạy được dù database đã restore hoặc failover thành công.
5. Có rủi ro ảnh hưởng production ngoài phạm vi drill.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Kiểm tra restore chain, Always On, listener, SQL Agent job và login mapping tại DR. |
| PostgreSQL | Kiểm tra PITR, WAL archive, promote replica, timeline và application connection. |
| MySQL MariaDB | Kiểm tra backup, binlog recovery, replica promotion, GTID và read write routing. |
| Oracle | Kiểm tra RMAN, Data Guard, listener, service name và application connectivity. |
| Azure SQL | Kiểm tra PITR, geo restore, failover group, private endpoint và Azure Monitor. |
| Z DB | Xác nhận cơ chế DR, backup, restore, HA và escalation theo nền tảng Z DB. |

## 12. Definition of Done

1. DR drill đã hoàn tất theo phạm vi đã định.
2. RTO và RPO thực tế đã được đo.
3. Kết quả validation được service owner hoặc app owner xác nhận.
4. DR Drill Report đã hoàn tất.
5. Action item đã có owner và deadline.

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
