# Database HA and DR Policy

## Thông tin tài liệu

1. Mã tài liệu: DBA POL 007
2. Loại tài liệu: Policy
3. Mức ưu tiên triển khai: 1
4. Owner đề xuất: DBA Team
5. Trạng thái: Draft
6. Phiên bản: 0.1
7. Phạm vi áp dụng: SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid
8. Chu kỳ review: 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật

## 1. Mục đích

Chính sách này chuẩn hóa yêu cầu về high availability và disaster recovery cho database. Mục tiêu là đảm bảo database critical có khả năng duy trì dịch vụ hoặc khôi phục dịch vụ trong giới hạn RTO và RPO đã được phê duyệt.

## 2. Phạm vi

Áp dụng cho database production và các hệ thống non production có vai trò trọng yếu trong release, kiểm thử hoặc vận hành. Chính sách áp dụng cho các mô hình như Always On, replication, Data Guard, streaming replication, cloud failover group, managed HA, backup restore DR và các mô hình tương đương.

## 3. Khái niệm

### 3.1. HA

HA là khả năng duy trì dịch vụ khi một thành phần gặp lỗi cục bộ, ví dụ primary node lỗi, instance lỗi, zone lỗi hoặc storage lỗi trong phạm vi kiến trúc HA.

### 3.2. DR

DR là khả năng khôi phục dịch vụ sau thảm họa lớn, ví dụ mất site, mất region, lỗi nghiêm trọng trên hạ tầng chính hoặc sự cố dữ liệu lớn.

### 3.3. Failover

Failover là chuyển vai trò primary sang standby hoặc replica khi primary không thể tiếp tục phục vụ.

### 3.4. Switchover

Switchover là chuyển vai trò có kế hoạch, thường dùng cho bảo trì hoặc kiểm thử.

### 3.5. RPO

RPO là mức mất dữ liệu tối đa có thể chấp nhận được tính theo thời gian.

### 3.6. RTO

RTO là thời gian tối đa để khôi phục dịch vụ.

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

### 5.1. Tier 1

Hệ thống trọng yếu, ảnh hưởng trực tiếp đến dịch vụ chính, khách hàng hoặc doanh thu. Cần HA rõ ràng, monitoring đầy đủ, RTO và RPO nghiêm ngặt.

### 5.2. Tier 2

Hệ thống quan trọng nhưng có thể chịu gián đoạn ngắn. Cần backup tin cậy, khả năng restore đã kiểm chứng và HA nếu được yêu cầu.

### 5.3. Tier 3

Hệ thống hỗ trợ hoặc nội bộ. Có thể phục hồi bằng backup trong thời gian dài hơn.

### 5.4. Tier 4

Hệ thống thử nghiệm hoặc không quan trọng. Không yêu cầu HA nếu không có phê duyệt đặc biệt.

## 6. Yêu cầu HA

1. Database Tier 1 phải có mô hình HA được phê duyệt.
2. HA phải có monitoring về node status, replication status, lag, quorum hoặc trạng thái tương đương.
3. HA phải có cảnh báo khi replica không đồng bộ hoặc không sẵn sàng.
4. Failover phải được kiểm tra định kỳ.
5. Sau failover, phải kiểm tra dữ liệu, kết nối ứng dụng và trạng thái replication.
6. Không triển khai HA chỉ trên giấy mà không có SOP vận hành.

## 7. Yêu cầu DR

1. Database Tier 1 phải có DR strategy.
2. DR strategy có thể là warm standby, cold standby, cross region backup, restore based DR hoặc mô hình khác được phê duyệt.
3. DR phải có runbook hoặc SOP.
4. DR drill phải đo RTO và RPO thực tế.
5. DR drill phải có báo cáo và action item.
6. Nếu DR drill thất bại, risk register phải được cập nhật.

## 8. Failover

### 8.1. Failover chủ động

Áp dụng khi bảo trì, kiểm thử hoặc di chuyển vai trò primary có kế hoạch.

Điều kiện tối thiểu:

1. Có RFC được phê duyệt.
2. Có maintenance window.
3. Có xác nhận của application team.
4. Replication hoặc synchronization ở trạng thái an toàn.
5. Có rollback hoặc fallback plan.
6. Có người theo dõi monitoring.

### 8.2. Failover bị động

Áp dụng khi primary lỗi hoặc không thể phục vụ.

Điều kiện tối thiểu:

1. Xác định impact.
2. Xác định primary không thể phục hồi trong RTO.
3. Có approval khẩn cấp theo operating model.
4. Kiểm tra replica hoặc standby có thể promote.
5. Thực hiện theo SOP failover.
6. Kiểm tra sau failover.
7. Lập PIR nếu sự cố nghiêm trọng.

## 9. Switchover

1. Switchover phải được lập kế hoạch.
2. Switchover phải có checklist trước thực hiện.
3. Switchover phải có app owner tham gia xác nhận.
4. Switchover phải có evidence trước và sau.
5. Switchover nên được dùng như một phần của HA drill định kỳ.

## 10. Replication

1. Replication lag phải được giám sát.
2. Replication break phải có alert.
3. Replica dùng cho read workload phải được đánh giá ảnh hưởng đến HA.
4. Replica không được coi là DR nếu cùng site và không đáp ứng yêu cầu thảm họa.
5. Quy trình rebuild replica phải có SOP hoặc runbook.

## 11. DR drill

DR drill phải bao gồm:

1. Kịch bản.
2. Phạm vi.
3. Database tham gia.
4. Người tham gia.
5. Recovery point.
6. Thời gian bắt đầu.
7. Thời gian kết thúc.
8. RTO thực tế.
9. RPO thực tế.
10. Kết quả kiểm tra ứng dụng.
11. Lỗi phát sinh.
12. Action item.

## 12. Trách nhiệm

1. DBA chịu trách nhiệm vận hành kỹ thuật database HA và DR.
2. Service owner chịu trách nhiệm phê duyệt RTO, RPO và rủi ro nghiệp vụ.
3. Application team chịu trách nhiệm xác nhận ứng dụng hoạt động sau failover hoặc DR.
4. Infra team chịu trách nhiệm hạ tầng, network, storage và cloud resource.
5. Security team tham gia khi DR hoặc failover liên quan dữ liệu nhạy cảm hoặc thay đổi quyền.
6. CAB phê duyệt switchover hoặc thay đổi có kế hoạch trên production.

## 13. Evidence bắt buộc

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

## 14. Chỉ số tuân thủ

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
