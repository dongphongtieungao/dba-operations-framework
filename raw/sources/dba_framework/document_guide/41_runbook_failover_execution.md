---
doc_id: DBA-RBK-041
title: "Runbook Failover Execution"
doc_type: runbook
priority: 1
status: ready_for_review
owner: DBA Team
reviewer: DBA Lead
approver: Service Owner
version: 1.0
created_date: 2026-05-15
last_updated: 2026-05-18
language: vi
framework: DBA Operations Framework
related_documents:
  - DBA-POL-007 Database HA and DR Policy
  - DBA-SOP-028 SOP HA Failover and Switchover
  - DBA-SOP-029 SOP Disaster Recovery Drill
  - DBA-TMP-048 Template Operation Evidence
  - DBA-TMP-050 Template Failover Drill Report
---

# DBA-RBK-041 - Runbook Failover Execution

## 1. Mục đích

Tự động hóa hoặc bán tự động hóa thao tác failover nhằm giảm sai sót, rút ngắn thời gian khôi phục và chuẩn hóa evidence.

## 2. Mô tả tóm tắt

Runbook này mô tả cách thực hiện promote, kiểm tra primary mới, kiểm tra replica, endpoint, application connection và log toàn bộ quá trình failover.

## 3. Phạm vi áp dụng

Runbook áp dụng cho planned switchover, unplanned failover, DR drill, HA test và tình huống khẩn cấp cần chuyển vai trò primary sang node hoặc site khác.

## 4. Nguyên tắc kiểm soát

1. Failover production là thao tác rủi ro cao, phải có phê duyệt hoặc emergency approval.
2. Không thực hiện failover nếu chưa xác định trạng thái dữ liệu và rủi ro mất dữ liệu, trừ tình huống khẩn cấp đã được approve.
3. Phải ghi lại timeline từng bước để phục vụ RCA hoặc báo cáo drill.
4. Sau failover phải kiểm tra database, replica, endpoint và application connection.
5. Nếu có thể, ưu tiên chạy qua script hoặc playbook đã kiểm thử thay vì thao tác thủ công.
6. Mọi failover phải có tiêu chí rollback hoặc fallback rõ ràng.

## 5. Phân loại kịch bản

| Kịch bản | Mô tả | Cơ chế phê duyệt |
|---|---|---|
| Planned switchover | Chuyển primary có kế hoạch, thường không mất dữ liệu | RFC đã phê duyệt |
| Unplanned failover | Primary lỗi, cần chuyển sang standby | Incident hoặc emergency change |
| DR failover | Chuyển sang site DR | DR approval hoặc crisis process |
| Failover drill | Diễn tập định kỳ | Kế hoạch drill đã phê duyệt |
| Partial failover | Chỉ chuyển một database, shard hoặc service | RFC hoặc service owner approval |

## 6. Điều kiện tiên quyết

| Hạng mục | Yêu cầu |
|---|---|
| HA topology | Có sơ đồ primary, standby, replica, listener, endpoint |
| Inventory | Có danh sách instance, database, service owner, application owner |
| Health check script | Có script kiểm tra node, replication, endpoint, application |
| Credential | Có tài khoản vận hành được cấp quyền thực hiện failover |
| Approval | Có RFC, incident approval hoặc emergency approval |
| Communication channel | Có bridge call, chat room hoặc kênh điều phối |
| Rollback plan | Có tiêu chí rollback hoặc fallback |
| Monitoring | Có dashboard theo dõi database và application |

## 7. Input bắt buộc

| Trường | Bắt buộc | Mô tả |
|---|---:|---|
| ticket_id | Có | RFC, incident hoặc emergency change |
| execution_id | Có | Mã lần chạy failover |
| failover_type | Có | Planned, unplanned, DR, drill |
| service_name | Có | Tên dịch vụ hoặc ứng dụng |
| dbms_type | Có | SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL, Z DB |
| current_primary | Có | Primary hiện tại |
| target_primary | Có | Node hoặc site dự kiến promote |
| rto_minutes | Có | RTO cam kết |
| rpo_minutes | Có | RPO cam kết |
| approver | Có | Người phê duyệt |
| communication_channel | Có | Kênh điều phối |

## 8. Output bắt buộc

| Trường | Mô tả |
|---|---|
| execution_id | Mã lần chạy |
| started_at | Thời điểm bắt đầu |
| ended_at | Thời điểm kết thúc |
| failover_type | Loại failover |
| old_primary | Primary cũ |
| new_primary | Primary mới |
| data_loss_estimate | Ước lượng mất dữ liệu nếu có |
| rto_actual_minutes | RTO thực tế |
| rpo_actual_minutes | RPO thực tế |
| database_health_result | PASS, WARN, FAIL |
| replica_health_result | PASS, WARN, FAIL |
| endpoint_result | PASS, WARN, FAIL |
| application_result | PASS, WARN, FAIL |
| final_result | SUCCESS, PARTIAL_SUCCESS, FAILED, ROLLED_BACK |
| evidence_path | Đường dẫn evidence |

## 9. Quy trình thực thi

### 9.1. Pre-check

1. Xác nhận ticket và approval.
2. Xác nhận phạm vi failover.
3. Xác nhận application owner đã biết thời điểm thực hiện.
4. Xác nhận trạng thái current primary.
5. Xác nhận trạng thái target primary.
6. Xác nhận replication lag hoặc redo apply lag.
7. Xác nhận backup gần nhất.
8. Xác nhận không có batch job quan trọng đang chạy, nếu policy yêu cầu.
9. Xác nhận monitoring dashboard đã sẵn sàng.
10. Xác nhận rollback hoặc fallback plan.

### 9.2. Freeze thay đổi

1. Tạm dừng change không liên quan.
2. Thông báo application team dừng deployment nếu cần.
3. Nếu planned switchover, cân nhắc drain connection hoặc chuyển ứng dụng sang maintenance mode.
4. Ghi nhận mốc thời gian freeze.

### 9.3. Thực hiện failover

Tùy DBMS, script hoặc playbook cần thực hiện:

1. Kiểm tra target đủ điều kiện promote.
2. Promote standby hoặc trigger failover.
3. Xác nhận primary role đã chuyển.
4. Cập nhật hoặc xác nhận listener, DNS, VIP, connection string, failover group hoặc endpoint.
5. Khởi động lại hoặc refresh connection pool nếu ứng dụng yêu cầu.
6. Kiểm tra replica còn lại.
7. Khởi tạo lại replica từ primary mới nếu cần.

### 9.4. Post-check database

Kiểm tra tối thiểu:

| Kiểm tra | Mục tiêu |
|---|---|
| New primary writable | Xác nhận primary mới ghi được |
| Old primary fenced | Tránh split brain |
| Replica connected | Xác nhận replica kết nối primary mới |
| Replication lag | Xác nhận lag trong ngưỡng |
| Data consistency | Kiểm tra dữ liệu mẫu hoặc checksum nếu có |
| Backup status | Xác nhận backup policy áp dụng cho primary mới |
| Monitoring status | Xác nhận dashboard nhận đúng primary mới |
| Alert status | Xác nhận không có alert critical tồn đọng |

### 9.5. Post-check application

1. Kiểm tra ứng dụng kết nối được database.
2. Kiểm tra chức năng đọc.
3. Kiểm tra chức năng ghi.
4. Kiểm tra transaction nghiệp vụ mẫu.
5. Kiểm tra log ứng dụng không có lỗi kết nối lặp lại.
6. Kiểm tra APM hoặc dashboard latency, error rate.
7. Xác nhận với application owner.

### 9.6. Kết thúc và bàn giao

1. Ghi nhận thời điểm hoàn tất.
2. Tính RTO thực tế.
3. Ước lượng RPO thực tế hoặc data loss nếu có.
4. Gửi kết quả cho stakeholder.
5. Lưu evidence.
6. Tạo action item nếu có lỗi.
7. Nếu là incident, chuyển sang PIR hoặc RCA.

## 10. Pseudocode tham khảo

```text
load failover request
validate approval
collect precheck metrics
if precheck critical fail and not emergency:
    stop execution
    notify stakeholders

freeze changes
record start time
execute failover command or playbook
verify new primary
verify endpoint
verify replicas
run database health check
run application smoke test
record end time
calculate RTO and RPO
store evidence
notify stakeholders
```

## 11. Gợi ý thao tác theo DBMS

### 11.1. SQL Server

Kiểm tra và thao tác thường gặp:

1. Always On Availability Group health.
2. Synchronous hoặc asynchronous commit mode.
3. Listener status.
4. Manual failover hoặc forced failover tùy tình huống.
5. SQL Agent jobs trên primary mới.
6. Login, linked server, credential nếu có.
7. Backup job chạy trên primary mới.

### 11.2. PostgreSQL

Kiểm tra và thao tác thường gặp:

1. Streaming replication status.
2. Replication lag.
3. Promote standby.
4. Kiểm tra timeline mới.
5. Cập nhật endpoint hoặc service discovery.
6. Rebuild hoặc rejoin old primary bằng pg rewind nếu phù hợp.
7. Kiểm tra replication slot.

### 11.3. MySQL hoặc MariaDB

Kiểm tra và thao tác thường gặp:

1. Replica lag.
2. GTID consistency.
3. Promote replica.
4. Set read only hoặc super read only.
5. Cập nhật router, proxy, DNS hoặc connection endpoint.
6. Repoint replicas sang primary mới.
7. Kiểm tra binlog continuity.

### 11.4. Oracle

Kiểm tra và thao tác thường gặp:

1. Data Guard status.
2. Transport lag và apply lag.
3. Switchover hoặc failover.
4. Broker configuration nếu dùng.
5. Listener và service name.
6. Kiểm tra open mode.
7. Flashback hoặc reinstate old primary nếu phù hợp.

### 11.5. Azure SQL

Kiểm tra và thao tác thường gặp:

1. Failover group.
2. Geo-replication status.
3. Private endpoint hoặc firewall.
4. DNS endpoint của failover group.
5. Application connection string.
6. Azure Monitor alert.
7. Zone redundancy hoặc geo restore nếu liên quan.

### 11.6. Z DB

Kiểm tra và thao tác cần phụ thuộc vào nền tảng:

1. HA topology được cung cấp bởi Z DB.
2. Quy trình promote hoặc failover theo tài liệu nền tảng.
3. Endpoint hoặc service binding.
4. Monitoring và alert của Z Cloud hoặc Z DB.
5. Escalation đến đội vận hành nền tảng nếu quyền failover không thuộc DBA team.

## 12. Điều kiện dừng

Dừng execution nếu gặp một trong các điều kiện sau, trừ khi emergency approver chấp nhận tiếp tục:

1. Không có approval hợp lệ.
2. Không xác định được target primary.
3. Target primary không đồng bộ vượt ngưỡng RPO.
4. Không xác định được replication state.
5. Có dấu hiệu split brain.
6. Không có rollback hoặc fallback plan cho planned switchover.
7. Application owner chưa sẵn sàng trong planned change.
8. Monitoring không hoạt động và không có phương án kiểm chứng thay thế.

## 13. Cổng quyết định trong khi failover

| Cổng | Câu hỏi bắt buộc | Kết quả |
|---|---|---|
| Go/No-go | Approval, owner, bridge, monitoring và rollback đã sẵn sàng chưa? | Go hoặc Stop |
| Pre-failover | Target primary/standby có đủ điều kiện promote không? | Continue hoặc Stop |
| Commit | Thao tác failover đã thực hiện và role mới đã xác nhận chưa? | Commit hoặc Escalate |
| Endpoint | Listener/DNS/VIP/failover group/connection path đã đúng chưa? | Continue hoặc Fix |
| Application | Ứng dụng đọc/ghi và transaction mẫu thành công chưa? | Handover hoặc Rollback/Fallback |
| Closure | RTO/RPO, evidence, stakeholder confirmation đã đủ chưa? | Close hoặc Action item |

## 14. Mẫu timeline tối thiểu

| Mốc thời gian | Nội dung cần ghi |
|---|---|
| T-30/T-15 | Bridge mở, stakeholder sẵn sàng, freeze change |
| T0 | Bắt đầu execution hoặc incident failover |
| T1 | Pre-check hoàn tất |
| T2 | Failover command/playbook bắt đầu |
| T3 | Primary mới xác nhận |
| T4 | Endpoint/connection path xác nhận |
| T5 | Database và replica post-check hoàn tất |
| T6 | Application smoke test hoàn tất |
| T7 | Bàn giao, tính RTO/RPO, lưu evidence |

## 15. Rollback hoặc fallback

| Tình huống | Hướng xử lý |
|---|---|
| Failover chưa commit và pre-check fail | Dừng execution, giữ primary hiện tại |
| New primary không writable | Cố gắng rollback nếu old primary còn an toàn |
| Endpoint không cập nhật được | Dùng phương án endpoint thủ công đã phê duyệt |
| Application không kết nối được | Rollback nếu trong cửa sổ cho phép, hoặc xử lý connection string |
| Replica không rejoin được | Bàn giao primary mới, tạo action item rebuild replica |
| Có nguy cơ split brain | Fence old primary ngay lập tức, escalate DBA Lead |

## 16. Evidence bắt buộc

| Evidence | Bắt buộc | Ghi chú |
|---|---:|---|
| Approval evidence | Có | RFC, incident approval hoặc emergency approval |
| Pre-check report | Có | Trạng thái trước failover |
| Execution log | Có | Log script hoặc playbook |
| Timeline | Có | Mốc thời gian từng bước |
| New primary verification | Có | Bằng chứng primary mới |
| Endpoint verification | Có | DNS, listener, VIP, failover group hoặc service endpoint |
| Application smoke test | Có | Bằng chứng ứng dụng hoạt động |
| Monitoring screenshot hoặc export | Có | Dashboard DB và app |
| RTO/RPO calculation | Có | Thực tế so với cam kết |
| Stakeholder confirmation | Có | Xác nhận của service owner hoặc app owner |

## 17. Escalation

| Tình huống | Escalation |
|---|---|
| Failover failed | DBA Lead, Service Owner, Incident Manager |
| Có khả năng mất dữ liệu | Service Owner, Business Owner |
| Split brain | DBA Lead, Infra Lead, Incident Manager |
| Endpoint không chuyển được | Infra, Network, Cloud Team |
| Ứng dụng không hoạt động sau failover | Application Team, Middleware Team |
| DR site không đạt RTO | Service Owner, DR Manager |
| Vấn đề bảo mật hoặc truy cập | Security Team |

## 18. Tiêu chí hoàn tất

Runbook được coi là hoàn tất khi:

1. Primary mới hoạt động đúng.
2. Endpoint hoặc connection path đã trỏ đúng.
3. Ứng dụng đọc và ghi thành công.
4. Replica còn lại đã kết nối hoặc có kế hoạch rebuild.
5. Monitoring và alert đã cập nhật đúng primary mới.
6. RTO và RPO thực tế đã được ghi nhận.
7. Evidence đã được lưu.
8. Stakeholder đã nhận thông báo hoàn tất.
9. Ticket được cập nhật kết quả.
10. Nếu là incident hoặc emergency change, PIR/RCA được khởi tạo.

## 19. Chỉ số liên quan

| KPI | Công thức tham khảo |
|---|---|
| Failover success rate | Successful failovers / Total failovers |
| RTO compliance rate | Failovers meeting RTO / Total failovers |
| RPO compliance rate | Failovers meeting RPO / Total failovers |
| Failover duration | End time - Start time |
| Application recovery time | App available time - Failover start time |
| Post-failover incident count | Incidents after failover / Total failovers |
