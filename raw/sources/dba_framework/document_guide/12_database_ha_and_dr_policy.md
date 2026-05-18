# Database HA and DR Policy

## Thông tin tài liệu

| Trường | Giá trị |
|--------|---------|
| Mã tài liệu | DBA-POL-007 |
| Loại tài liệu | Policy |
| Mức ưu tiên triển khai | 1 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Infra Lead |
| Approver | Service Owner |
| Trạng thái | Draft |
| Phiên bản | 0.2 |
| Ngày tạo | 2026-05-18 |
| Ngày review gần nhất | 2026-05-18 |
| Ngày review tiếp theo | 2026-11-18 |
| Phạm vi áp dụng | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng |

### Lịch sử thay đổi

| Phiên bản | Ngày | Người thay đổi | Mô tả |
|-----------|------|----------------|-------|
| 0.1 | 2026-05-18 | DBA Team | Bản draft đầu tiên |
| 0.2 | 2026-05-18 | DBA Team | Bổ sung HA pattern table, split-brain, RTO/RPO decision matrix, metadata |

## 1. Mục đích

Chính sách này chuẩn hóa yêu cầu về high availability và disaster recovery cho database. Mục tiêu là đảm bảo database critical có khả năng duy trì dịch vụ hoặc khôi phục dịch vụ trong giới hạn RTO và RPO đã được phê duyệt.

## 2. Phạm vi

Áp dụng cho database production và các hệ thống non production có vai trò trọng yếu. Chính sách áp dụng cho các mô hình như Always On, replication, Data Guard, streaming replication, cloud failover group, managed HA, backup restore DR và các mô hình tương đương.

## 3. Khái niệm

| Thuật ngữ | Định nghĩa |
|-----------|-----------|
| HA | Khả năng duy trì dịch vụ khi một thành phần gặp lỗi cục bộ |
| DR | Khả năng khôi phục dịch vụ sau thảm họa lớn |
| Failover | Chuyển vai trò primary sang standby khi primary không thể phục vụ |
| Switchover | Chuyển vai trò có kế hoạch, thường dùng cho bảo trì |
| RPO | Mức mất dữ liệu tối đa chấp nhận được tính theo thời gian |
| RTO | Thời gian tối đa để khôi phục dịch vụ |

## 4. Nguyên tắc

1. Mỗi database production phải được phân loại criticality.
2. Database critical phải có RTO và RPO được phê duyệt.
3. HA không thay thế backup.
4. Backup không thay thế HA nếu yêu cầu RTO thấp.
5. DR không được coi là sẵn sàng nếu chưa diễn tập.
6. Failover hoặc switchover production phải có SOP và evidence.
7. Ứng dụng phải được kiểm tra tương thích với mô hình HA.
8. Connection string, DNS, endpoint hoặc listener phải được thiết kế để hỗ trợ chuyển đổi.

## 5. Phân loại mức criticality

| Tier | Mô tả | RTO | RPO | HA | DR |
|------|-------|-----|-----|----|----|
| Tier 1 | Hệ thống trọng yếu, ảnh hưởng dịch vụ chính, khách hàng, doanh thu | ≤ 1 giờ | ≤ 5 phút | Bắt buộc | Bắt buộc |
| Tier 2 | Hệ thống quan trọng, chịu gián đoạn ngắn | ≤ 4 giờ | ≤ 1 giờ | Khuyến nghị | Khuyến nghị |
| Tier 3 | Hệ thống hỗ trợ hoặc nội bộ | ≤ 24 giờ | ≤ 24 giờ | Tùy chọn | Tùy chọn |
| Tier 4 | Hệ thống thử nghiệm | Best effort | Best effort | Không yêu cầu | Không yêu cầu |

## 6. HA architecture pattern tham khảo

| DBMS | HA Pattern | Sync Mode | DR Pattern |
|------|-----------|-----------|------------|
| SQL Server | Always On AG | Synchronous commit | Always On AG async cross-region |
| PostgreSQL | Streaming Replication + Patroni/Citus | Sync hoặc async | Cross-region replica + WAL archive |
| MySQL | InnoDB Cluster hoặc Group Replication | Sync hoặc semi-sync | Async replica + binlog backup |
| MariaDB | Galera Cluster hoặc Replication | Sync hoặc async | Async replica + binlog backup |
| Oracle | Data Guard | Sync hoặc far sync | Data Guard async cross-region |
| Azure SQL DB | Built-in HA | Automatic | Geo-replication hoặc auto-failover group |
| Azure SQL MI | Built-in HA | Automatic | Auto-failover group |

> **DBMS Implementation**: Xem chi tiết HA/DR implementation cho từng DBMS tại các Appendix: SQL Server (DBA-APP-001), PostgreSQL (DBA-APP-002), MySQL/MariaDB (DBA-APP-003), Oracle (DBA-APP-004), Azure SQL (DBA-APP-005).

## 7. Yêu cầu HA

1. Database Tier 1 phải có mô hình HA được phê duyệt.
2. HA phải có monitoring về node status, replication status, lag, quorum.
3. HA phải có cảnh báo khi replica không đồng bộ hoặc không sẵn sàng.
4. Failover phải được kiểm tra định kỳ.
5. Sau failover, phải kiểm tra dữ liệu, kết nối ứng dụng và trạng thái replication.
6. Không triển khai HA chỉ trên giấy mà không có SOP vận hành.

## 8. Yêu cầu DR

1. Database Tier 1 phải có DR strategy.
2. DR strategy có thể là warm standby, cold standby, cross region backup, restore based DR hoặc mô hình khác được phê duyệt.
3. DR phải có runbook hoặc SOP.
4. DR drill phải đo RTO và RPO thực tế.
5. DR drill phải có báo cáo và action item.
6. Nếu DR drill thất bại, risk register phải được cập nhật.

## 9. Failover

### 9.1. Failover chủ động

Áp dụng khi bảo trì, kiểm thử hoặc di chuyển vai trò primary có kế hoạch.

Điều kiện tối thiểu:

1. Có RFC được phê duyệt.
2. Có maintenance window.
3. Có xác nhận của application team.
4. Replication ở trạng thái an toàn.
5. Có rollback hoặc fallback plan.
6. Có người theo dõi monitoring.

### 9.2. Failover bị động

Áp dụng khi primary lỗi hoặc không thể phục vụ.

Điều kiện tối thiểu:

1. Xác định impact.
2. Xác định primary không thể phục hồi trong RTO.
3. Có approval khẩn cấp theo operating model.
4. Kiểm tra replica có thể promote.
5. Thực hiện theo SOP failover.
6. Kiểm tra sau failover.
7. Lập PIR nếu sự cố nghiêm trọng.

## 10. Split-brain handling

1. Khi xảy ra split-brain, ưu tiên data integrity hơn availability.
2. Không cho phép hai node cùng nhận write nếu quorum không đảm bảo.
3. DBA phải có SOP xác định node nào là primary hợp lệ khi split-brain được phát hiện.
4. Node không hợp lệ phải được isolate trước khi rejoin cluster.
5. Sau khi giải quyết split-brain, phải kiểm tra data consistency giữa các node.

## 11. Switchover

1. Switchover phải được lập kế hoạch.
2. Switchover phải có checklist trước thực hiện.
3. Switchover phải có app owner tham gia xác nhận.
4. Switchover phải có evidence trước và sau.
5. Switchover nên được dùng như một phần của HA drill định kỳ.

## 12. Replication

1. Replication lag phải được giám sát.
2. Replication break phải có alert.
3. Replica dùng cho read workload phải được đánh giá ảnh hưởng đến HA.
4. Replica không được coi là DR nếu cùng site.
5. Quy trình rebuild replica phải có SOP hoặc runbook.

## 13. DR drill

DR drill phải bao gồm:

1. Kịch bản.
2. Phạm vi.
3. Database tham gia.
4. Người tham gia.
5. Recovery point.
6. Thời gian bắt đầu và kết thúc.
7. RTO thực tế.
8. RPO thực tế.
9. Kết quả kiểm tra ứng dụng.
10. Lỗi phát sinh.
11. Action item.

## 14. Trách nhiệm

1. DBA chịu trách nhiệm vận hành kỹ thuật database HA và DR.
2. Service owner phê duyệt RTO, RPO và rủi ro nghiệp vụ.
3. Application team xác nhận ứng dụng hoạt động sau failover hoặc DR.
4. Infra team chịu trách nhiệm hạ tầng, network, storage.
5. Security team tham gia khi liên quan dữ liệu nhạy cảm.
6. CAB phê duyệt switchover hoặc thay đổi có kế hoạch trên production.

## 15. Evidence bắt buộc

1. Trạng thái trước failover hoặc DR.
2. Ticket hoặc RFC.
3. Approval.
4. Log thao tác.
5. Thời gian bắt đầu và kết thúc.
6. Trạng thái primary mới.
7. Trạng thái replica.
8. Kết quả kiểm tra kết nối ứng dụng.
9. Kết quả kiểm tra dữ liệu.
10. Monitoring evidence.
11. Action item nếu có lỗi.

## 16. Chỉ số tuân thủ

1. Tỷ lệ database Tier 1 có HA design.
2. Tỷ lệ database Tier 1 có RTO và RPO được phê duyệt.
3. Failover drill success rate.
4. DR drill success rate.
5. RTO compliance rate.
6. RPO compliance rate.
7. Replication lag violation count.
8. HA incident count.
9. DR risk count.
10. Action item completion rate sau drill.

## 17. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-POL-005 | Backup and Restore Policy | RPO, RTO, restore |
| DBA-OM-003 | DBA RACI Matrix | Trách nhiệm HA DR |
| DBA-OM-002 | DBA Operating Model | Escalation failover |
| DBA-POL-006 | Database Audit and Compliance Policy | Audit HA DR event |
