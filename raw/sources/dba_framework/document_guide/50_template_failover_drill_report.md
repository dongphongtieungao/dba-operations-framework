---
status: draft
framework: DBA Operations Framework
document_group: Templates and Metrics
doc_id: DBA-TMP-050
language: vi
---

# Template Failover Drill Report

## 1. Thông tin tài liệu

| Trường | Giá trị |
|---|---|
| Mã tài liệu | DBA-TMP-050 |
| Tên tài liệu | Template Failover Drill Report |
| Nhóm tài liệu | Template and Metrics |
| Mức ưu tiên | 1 |
| Mục đích | Chuẩn hóa báo cáo diễn tập failover |
| Trạng thái | Draft |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Service Owner, Infra Lead, Security Lead khi liên quan |
| Chu kỳ rà soát | Hằng quý hoặc sau mỗi sự cố lớn, thay đổi quy trình, thay đổi công cụ |

## 2. Mục đích

Tài liệu này chuẩn hóa báo cáo diễn tập failover hoặc switchover để kiểm chứng năng lực HA, đo downtime thực tế, xác nhận trạng thái dữ liệu, xác nhận endpoint ứng dụng và tạo action item cải tiến.

## 3. Phạm vi áp dụng

| Loại drill | Mô tả |
|---|---|
| Planned switchover | Chuyển vai trò chủ động trong bảo trì |
| Planned failover drill | Diễn tập failover theo kịch bản đã chuẩn bị |
| DR failover drill | Chuyển sang site hoặc region DR |
| Read replica promotion drill | Promote replica thành primary theo quy trình |
| Application connection failover test | Kiểm tra ứng dụng kết nối lại qua listener, DNS hoặc failover group |

## 4. Thông tin chung

| Trường | Nội dung |
|---|---|
| Drill ID |  |
| Related ticket hoặc change |  |
| Drill date |  |
| Người thực hiện |  |
| Người quan sát hoặc reviewer |  |
| Service owner |  |
| Application, service |  |
| Environment | Production, DR, Staging, UAT, Development |
| DBMS, version |  |
| Cluster, instance, database |  |
| Primary trước drill |  |
| Secondary hoặc standby trước drill |  |
| SOP sử dụng | SOP HA Failover and Switchover |
| Runbook sử dụng nếu có |  |

## 5. Mục tiêu drill

| Mục tiêu | Giá trị mục tiêu | Kết quả thực tế | Đạt, không đạt |
|---|---|---|---|
| Kiểm chứng quy trình failover |  |  |  |
| Đo downtime thực tế |  |  |  |
| Đo RTO thực tế |  |  |  |
| Đo RPO thực tế nếu có |  |  |  |
| Kiểm tra dữ liệu sau failover |  |  |  |
| Kiểm tra ứng dụng kết nối lại |  |  |  |
| Kiểm tra monitoring và alert |  |  |  |

## 6. Trạng thái trước failover

| Kiểm tra | Kết quả mong muốn | Kết quả thực tế | Evidence |
|---|---|---|---|
| Primary healthy | Healthy |  |  |
| Secondary, standby, replica healthy | Healthy |  |  |
| Replication lag | Trong ngưỡng chấp nhận |  |  |
| Backup gần nhất | Thành công |  |  |
| Disk, log, WAL, tablespace | Đủ dung lượng |  |  |
| Application connection baseline | Hoạt động bình thường |  |  |
| Monitoring dashboard | Không có alert xung đột |  |  |
| Runbook đã được xác nhận | Sẵn sàng |  |  |

## 7. Timeline failover

| Thời gian | Sự kiện | Người thực hiện | Evidence |
|---|---|---|---|
| YYYY-MM-DD HH:MM TZ | Bắt đầu drill |  |  |
| YYYY-MM-DD HH:MM TZ | Thông báo bắt đầu |  |  |
| YYYY-MM-DD HH:MM TZ | Thực hiện freeze hoặc stop write nếu cần |  |  |
| YYYY-MM-DD HH:MM TZ | Bắt đầu failover hoặc switchover |  |  |
| YYYY-MM-DD HH:MM TZ | Primary mới sẵn sàng |  |  |
| YYYY-MM-DD HH:MM TZ | Endpoint, DNS, listener hoặc connection string sẵn sàng |  |  |
| YYYY-MM-DD HH:MM TZ | Application smoke test pass |  |  |
| YYYY-MM-DD HH:MM TZ | Drill hoàn tất |  |  |

## 8. Kết quả failover

| Trường | Nội dung |
|---|---|
| Failover status | Success, Partial Success, Failed, Rolled Back |
| Downtime thực tế |  |
| RTO target |  |
| RTO actual |  |
| RPO target |  |
| RPO actual |  |
| Primary sau drill |  |
| Secondary hoặc replica sau drill |  |
| Có mất dữ liệu không | Yes, No, Unknown |
| Có cần failback không | Yes, No |
| Có cần mở incident không | Yes, No |

## 9. Kiểm tra sau failover

| # | Kiểm tra | Kết quả mong muốn | Kết quả thực tế | Evidence |
|---:|---|---|---|---|
| 1 | Database role | Primary, secondary đúng như thiết kế |  |  |
| 2 | Database online | Online |  |  |
| 3 | Replication sau failover | Hoạt động hoặc có kế hoạch tái lập |  |  |
| 4 | Endpoint, listener, DNS | Trỏ đúng primary mới |  |  |
| 5 | Application connection | Kết nối thành công |  |  |
| 6 | Smoke test nghiệp vụ | Pass |  |  |
| 7 | Error log | Không có lỗi nghiêm trọng |  |  |
| 8 | Monitoring và alert | Cập nhật đúng trạng thái |  |  |
| 9 | Backup sau failover | Policy vẫn hoạt động hoặc đã kích hoạt lại |  |  |

## 10. Lỗi phát sinh

| Lỗi | Thời điểm | Tác động | Cách xử lý | Người xử lý | Trạng thái |
|---|---|---|---|---|---|
|  |  |  |  |  | Open, Closed |

## 11. Kết luận drill

| Nội dung | Kết quả |
|---|---|
| Failover drill đạt yêu cầu không | Yes, No, Partially |
| Dịch vụ có phục hồi trong RTO không | Yes, No, N/A |
| Dữ liệu có đáp ứng RPO không | Yes, No, N/A |
| Ứng dụng có kết nối lại thành công không | Yes, No, Partially |
| Runbook đủ rõ để vận hành không | Yes, No, Partially |
| Monitoring phát hiện đúng trạng thái không | Yes, No, Partially |
| Cần diễn tập lại không | Yes, No |

## 12. Action item cải tiến

| ID | Action item | Priority | Owner | Due date | Success criteria | Status |
|---|---|---|---|---|---|---|
| FD-001 |  | P1, P2, P3 |  |  |  | Open |

## 13. Tài liệu cần cập nhật

| Tài liệu | Có cần cập nhật | Nội dung cập nhật |
|---|---|---|
| Database HA and DR Policy | Yes, No |  |
| SOP HA Failover and Switchover | Yes, No |  |
| SOP Replication Troubleshooting | Yes, No |  |
| Runbook Failover Execution | Yes, No |  |
| Template Operation Evidence | Yes, No |  |
| DBA Risk Register | Yes, No |  |

## 99. Lịch sử thay đổi

| Version | Ngày | Người cập nhật | Nội dung thay đổi |
|---|---|---|---|
| 0.1 | 2026-05-18 | DBA Team | Khởi tạo bản nháp |
| 0.2 | 2026-05-18 | DBA Team | Chuẩn hóa doc ID |
