---
status: draft
framework: DBA Operations Framework
document_group: Templates and Metrics
doc_id: DBA-MET-052
language: vi
---

# DBA KPI and Metrics Catalog

## 1. Thông tin tài liệu

| Trường | Giá trị |
|---|---|
| Mã tài liệu | DBA-MET-052 |
| Tên tài liệu | DBA KPI and Metrics Catalog |
| Nhóm tài liệu | Template and Metrics |
| Mức ưu tiên | 2 |
| Mục đích | Định nghĩa chỉ số đo vận hành DBA |
| Trạng thái | Draft |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Service Owner, Infra Lead, Security Lead khi liên quan |
| Chu kỳ rà soát | Hằng quý hoặc sau mỗi sự cố lớn, thay đổi quy trình, thay đổi công cụ |

## 2. Mục đích

Tài liệu này định nghĩa bộ chỉ số đo vận hành DBA. Mục tiêu là chuyển hoạt động DBA từ xử lý thủ công cảm tính sang vận hành có số liệu, có baseline, có xu hướng, có ngưỡng cảnh báo và có cơ sở cải tiến.

## 3. Nguyên tắc định nghĩa KPI

1. KPI phải có công thức rõ ràng.
2. KPI phải có nguồn dữ liệu có thể kiểm tra lại.
3. KPI phải có owner chịu trách nhiệm theo dõi.
4. KPI phải có chu kỳ đo.
5. KPI không được khuyến khích hành vi sai, ví dụ đóng ticket nhanh nhưng không xử lý root cause.
6. Target cụ thể phải do service owner, DBA lead và SLA thống nhất. Tài liệu này không tự giả định target cố định.

## 4. Nhóm chỉ số reliability

| KPI | Mục đích | Công thức gợi ý | Nguồn dữ liệu | Chu kỳ | Owner |
|---|---|---|---|---|---|
| Database availability | Đo mức sẵn sàng database | Thời gian available chia cho tổng thời gian trong kỳ | Monitoring, SLA report | Monthly | DBA Team, Service Owner |
| Unplanned downtime | Đo thời gian downtime không kế hoạch | Tổng thời gian outage ngoài kế hoạch | Incident, monitoring | Monthly | DBA Team |
| Replication lag violation count | Đo số lần replica vượt ngưỡng lag | Count sự kiện lag vượt threshold đã thống nhất | Monitoring | Daily, Monthly | DBA Team |
| HA health compliance | Đo tỷ lệ HA ở trạng thái healthy | Số cụm HA healthy chia cho tổng cụm HA trong phạm vi | Monitoring, health check | Weekly, Monthly | DBA Team |
| DR readiness status | Đánh giá sẵn sàng DR | Green, Yellow, Red dựa trên drill, replication, backup, runbook | DR report, monitoring | Monthly | DBA Lead |

## 5. Nhóm chỉ số backup và restore

| KPI | Mục đích | Công thức gợi ý | Nguồn dữ liệu | Chu kỳ | Owner |
|---|---|---|---|---|---|
| Backup success rate | Đo tỷ lệ backup thành công | Số backup thành công chia cho tổng backup expected | Backup system, job log | Daily, Monthly | DBA Team |
| Backup overdue count | Phát hiện database chưa có backup đúng hạn | Count database có backup age vượt ngưỡng policy | Backup inventory | Daily | DBA Team |
| Restore success rate | Đo khả năng restore thành công | Số restore thành công chia cho tổng restore thực hiện | Restore ticket, drill report | Monthly | DBA Team |
| Restore drill pass rate | Đo chất lượng diễn tập restore | Số restore drill đạt chia cho tổng restore drill | Restore drill report | Monthly, Quarterly | DBA Lead |
| RPO compliance rate | Đo khả năng đáp ứng RPO | Số restore hoặc drill đạt RPO chia cho tổng restore hoặc drill | Drill report, incident | Monthly, Quarterly | DBA Lead, Service Owner |
| RTO compliance rate | Đo khả năng đáp ứng RTO | Số restore hoặc drill đạt RTO chia cho tổng restore hoặc drill | Drill report, incident | Monthly, Quarterly | DBA Lead, Service Owner |
| Backup storage risk count | Đo rủi ro thiếu dung lượng backup | Count location có utilization vượt ngưỡng | Storage monitoring | Weekly, Monthly | DBA Team, Infra Team |

## 6. Nhóm chỉ số incident management

| KPI | Mục đích | Công thức gợi ý | Nguồn dữ liệu | Chu kỳ | Owner |
|---|---|---|---|---|---|
| Incident count by severity | Theo dõi số lượng sự cố | Count incident theo P1 đến P5 | ITSM | Weekly, Monthly | DBA Manager |
| MTTA | Đo thời gian phản hồi | Trung bình thời gian từ alert hoặc ticket đến khi được nhận xử lý | ITSM, alerting | Monthly | DBA Manager |
| MTTR | Đo thời gian khôi phục | Trung bình thời gian từ phát hiện đến khôi phục dịch vụ | ITSM, PIR | Monthly | DBA Manager |
| Repeated incident count | Đo sự cố lặp lại | Count incident cùng pattern hoặc cùng root cause | ITSM, PIR | Monthly | DBA Lead |
| PIR completion rate | Đo tuân thủ phân tích sau sự cố | Số PIR hoàn tất chia cho số PIR required | PIR tracker | Monthly | DBA Lead |
| Corrective action overdue count | Theo dõi action item trễ hạn | Count action item quá due date | PIR tracker, backlog | Weekly, Monthly | DBA Lead |

## 7. Nhóm chỉ số change management

| KPI | Mục đích | Công thức gợi ý | Nguồn dữ liệu | Chu kỳ | Owner |
|---|---|---|---|---|---|
| Change success rate | Đo chất lượng change | Số change thành công chia cho tổng change | ITSM | Monthly | DBA Lead |
| Rollback rate | Đo tỷ lệ rollback | Số change rollback chia cho tổng change | ITSM | Monthly | DBA Lead |
| Emergency change ratio | Đo mức độ thay đổi khẩn cấp | Số emergency change chia cho tổng change | ITSM | Monthly | DBA Manager |
| Unauthorized change count | Đo vi phạm quy trình | Count change không có approval hoặc không có ticket | Audit, ITSM | Monthly | DBA Manager, Security |
| Change caused incident count | Đo change gây sự cố | Count incident có root cause là change | PIR, ITSM | Monthly | DBA Lead |
| RFC evidence completion rate | Đo đầy đủ evidence change | Số RFC có evidence đủ chia cho tổng RFC | ITSM | Monthly | DBA Lead |

## 8. Nhóm chỉ số security và compliance

| KPI | Mục đích | Công thức gợi ý | Nguồn dữ liệu | Chu kỳ | Owner |
|---|---|---|---|---|---|
| Privileged access count | Theo dõi quyền cao | Count account có quyền cao theo policy | IAM, database catalog | Weekly, Monthly | DBA Team, Security |
| Expired access not removed count | Phát hiện quyền hết hạn chưa thu hồi | Count access quá hạn vẫn còn active | Access review, database catalog | Weekly | DBA Team |
| Failed login anomaly count | Phát hiện đăng nhập bất thường | Count event vượt baseline hoặc threshold | Audit log, SIEM | Daily, Monthly | Security, DBA Team |
| Audit coverage rate | Đo mức bật audit | Số database có audit theo yêu cầu chia cho tổng database trong phạm vi | Audit config inventory | Monthly | Security, DBA Team |
| Data masking compliance rate | Đo tuân thủ masking non production | Số môi trường đạt masking chia cho tổng môi trường yêu cầu masking | Masking report, review | Monthly, Quarterly | DBA Team, Data Owner |
| Permission review completion rate | Đo rà soát quyền định kỳ | Số review hoàn tất chia cho số review planned | Access review tracker | Quarterly | DBA Lead, Security |

## 9. Nhóm chỉ số performance và capacity

| KPI | Mục đích | Công thức gợi ý | Nguồn dữ liệu | Chu kỳ | Owner |
|---|---|---|---|---|---|
| Slow query ticket count | Theo dõi vấn đề query chậm | Count ticket performance query | ITSM, APM | Monthly | DBA Team |
| Blocking incident count | Theo dõi blocking nghiêm trọng | Count incident hoặc alert blocking vượt ngưỡng | Monitoring, ITSM | Monthly | DBA Team |
| Deadlock count | Theo dõi deadlock | Count deadlock event | Database log, monitoring | Daily, Monthly | DBA Team |
| Resource saturation event count | Theo dõi CPU, memory, IO, connection, temp space quá tải | Count event vượt threshold | Monitoring | Daily, Monthly | DBA Team, Infra Team |
| Capacity forecast risk count | Đo rủi ro hết dung lượng | Count database hoặc storage dự kiến vượt ngưỡng trong kỳ forecast | Capacity report | Monthly | DBA Team, Infra Team |
| Tuning recommendation implementation rate | Đo tỷ lệ khuyến nghị tuning được triển khai | Số recommendation implemented chia cho tổng recommendation approved | Backlog, ITSM | Monthly | DBA Lead, App Team |

## 10. Nhóm chỉ số automation và documentation

| KPI | Mục đích | Công thức gợi ý | Nguồn dữ liệu | Chu kỳ | Owner |
|---|---|---|---|---|---|
| SOP coverage rate | Đo mức chuẩn hóa quy trình | Số service có SOP chia cho tổng service trong catalog | Service catalog, KMS | Monthly | DBA Lead |
| Runbook coverage rate | Đo mức tự động hóa | Số SOP có runbook chia cho tổng SOP đủ điều kiện automation | KMS, repository | Monthly | DBA Lead, DevOps |
| Automation execution rate | Đo tỷ lệ task chạy bằng automation | Số ticket xử lý bằng runbook chia cho tổng ticket đủ điều kiện | ITSM, pipeline log | Monthly | DBA Lead |
| Manual production operation count | Đo ClickOps còn lại | Count thao tác production thủ công | Evidence, audit log | Monthly | DBA Manager |
| Automation failure count | Đo lỗi automation | Count runbook hoặc pipeline failed | Pipeline, job log | Monthly | DBA Lead, DevOps |
| Document review compliance | Đo tuân thủ review tài liệu | Số tài liệu review đúng hạn chia cho tổng tài liệu đến hạn | KMS metadata | Monthly, Quarterly | DBA Lead |

## 11. Nhóm chỉ số service request

| KPI | Mục đích | Công thức gợi ý | Nguồn dữ liệu | Chu kỳ | Owner |
|---|---|---|---|---|---|
| Service request volume | Đo khối lượng yêu cầu DBA | Count SR theo loại | ITSM | Monthly | DBA Manager |
| Service request SLA compliance | Đo tuân thủ SLA | Số SR hoàn tất trong SLA chia cho tổng SR | ITSM | Monthly | DBA Manager |
| Reopen rate | Đo chất lượng xử lý ticket | Số ticket reopen chia cho số ticket closed | ITSM | Monthly | DBA Manager |
| Request rejection rate | Đo chất lượng input từ requester | Số request bị trả lại do thiếu thông tin chia cho tổng request | ITSM | Monthly | DBA Lead |
| First time right rate | Đo khả năng xử lý đúng ngay lần đầu | Số request hoàn tất không cần bổ sung hoặc sửa lại chia cho tổng request | ITSM | Monthly | DBA Lead |

## 12. KPI dashboard tối thiểu

| Dashboard | Nội dung nên có | Đối tượng xem |
|---|---|---|
| DBA Executive Dashboard | Availability, incident, change, backup, restore, top risk | Manager, Service Owner |
| DBA Reliability Dashboard | HA, replication, RPO, RTO, downtime, DR readiness | DBA Lead, Infra Lead |
| DBA Backup Restore Dashboard | Backup success, overdue, restore drill, RPO, RTO | DBA Team |
| DBA Security Dashboard | Privileged access, failed login, audit coverage, masking | DBA Team, Security |
| DBA Automation Dashboard | SOP coverage, runbook coverage, manual operation, automation failure | DBA Lead, DevOps |
| DBA Capacity Dashboard | Growth, disk, log, temp, IOPS, forecast risk | DBA Team, Infra Team |

## 13. Cách sử dụng KPI trong cải tiến vận hành

1. Mỗi KPI phải có baseline trước khi đặt target cứng.
2. KPI có trạng thái Red phải có action item và owner.
3. KPI xấu liên tục nhiều kỳ phải được đưa vào Problem Management hoặc Improvement Backlog.
4. KPI không có nguồn dữ liệu đáng tin cậy phải được đánh dấu là measurement gap.
5. Không dùng KPI đơn lẻ để đánh giá cá nhân nếu quy trình, công cụ hoặc workload chưa được chuẩn hóa.

## 14. Measurement gap log

| Gap ID | KPI bị ảnh hưởng | Thiếu dữ liệu gì | Nguồn dữ liệu cần bổ sung | Owner | Due date | Status |
|---|---|---|---|---|---|---|
| MG-001 |  |  |  |  |  | Open |

## 99. Lịch sử thay đổi

| Version | Ngày | Người cập nhật | Nội dung thay đổi |
|---|---|---|---|
| 0.1 | 2026-05-18 | DBA Team | Khởi tạo bản nháp |
| 0.2 | 2026-05-18 | DBA Team | Chuẩn hóa doc ID |
