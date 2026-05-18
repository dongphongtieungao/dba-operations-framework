# DBA Service Catalog

## Thông tin tài liệu

| Trường | Giá trị |
|--------|---------|
| Mã tài liệu | DBA-OM-001 |
| Loại tài liệu | Operating Model |
| Mức ưu tiên triển khai | 3 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Infra Lead |
| Approver | Service Owner |
| Trạng thái | Draft |
| Phiên bản | 0.2 |
| Ngày tạo | 2026-05-18 |
| Ngày review gần nhất | 2026-05-18 |
| Ngày review tiếp theo | 2026-11-18 |
| Phạm vi áp dụng | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật |

### Lịch sử thay đổi

| Phiên bản | Ngày | Người thay đổi | Mô tả |
|-----------|------|----------------|-------|
| 0.1 | 2026-05-18 | DBA Team | Bản draft đầu tiên |
| 0.2 | 2026-05-18 | DBA Team | Bổ sung summary table, OLA, service mới, chuẩn hóa metadata |

## 1. Mục đích

Tài liệu này chuẩn hóa danh mục dịch vụ DBA cung cấp cho tổ chức. Service Catalog giúp requester, application team, infra team, security team và DBA team hiểu rõ mỗi dịch vụ cần input gì, output là gì, SLA dự kiến, tài liệu SOP nào được dùng và bằng chứng nào phải lưu lại.

## 2. Nguyên tắc quản lý dịch vụ DBA

1. Mọi dịch vụ DBA phải có mã dịch vụ.
2. Mọi dịch vụ có tác động production phải có ticket.
3. Dịch vụ rủi ro cao phải có approval và evidence bắt buộc.
4. Dịch vụ lặp lại nhiều lần phải có SOP.
5. Dịch vụ có SOP ổn định phải được đánh giá để tự động hóa.
6. Dịch vụ không còn sử dụng phải được archive khỏi catalog sau khi review.

## 3. Bảng tổng hợp dịch vụ

| Service ID | Dịch vụ | SOP | Runbook | SLA | OLA |
|-----------|---------|-----|---------|-----|-----|
| SR-DBA-001 | Tạo database mới | SOP Provisioning | RBK Database Provisioning | 2 ngày | Infra cung cấp VM: 1 ngày |
| SR-DBA-002 | Cấp quyền database | SOP User Permission | RBK User Provisioning | 1 ngày | Security approve: 4 giờ |
| SR-DBA-003 | Restore dữ liệu | SOP Restore Operation | RBK Restore Validation | Theo severity | N/A |
| SR-DBA-004 | Query tuning support | SOP Performance Troubleshooting | N/A | Theo severity | App cung cấp query: 4 giờ |
| SR-DBA-005 | Failover hoặc switchover | SOP HA Failover | RBK Failover Execution | Theo HA/DR policy | Infra kiểm tra network: 30 phút |
| SR-DBA-006 | Review schema change | SOP Schema Change Review | N/A | 2 ngày | App cung cấp DDL: khi submit |
| SR-DBA-007 | Certificate hoặc key rotation | SOP Certificate Rotation | N/A | 5 ngày | Security cung cấp cert: 2 ngày |
| SR-DBA-008 | Capacity assessment | SOP Capacity Management | RBK Capacity Report | 5 ngày | Infra cung cấp storage info: 1 ngày |

## 4. Nhóm dịch vụ DBA

### 4.1. Provisioning

1. Tạo database mới.
2. Tạo schema.
3. Tạo user hoặc role.
4. Cấp quyền.
5. Bổ sung replica.
6. Đăng ký monitoring và alerting cho database mới.

### 4.2. Backup and restore

1. Kiểm tra backup.
2. Tạo backup ad hoc.
3. Restore dữ liệu.
4. Restore theo thời điểm.
5. Restore sang môi trường non production.
6. Diễn tập restore.

### 4.3. HA and DR

1. Kiểm tra trạng thái HA.
2. Thực hiện switchover có kế hoạch.
3. Thực hiện failover khẩn cấp.
4. Diễn tập DR.
5. Kiểm tra replication lag.
6. Bổ sung hoặc thay thế replica.

### 4.4. Performance and capacity

1. Điều tra query chậm.
2. Review execution plan.
3. Đề xuất index.
4. Kiểm tra blocking và deadlock.
5. Phân tích capacity.
6. Đề xuất scale up hoặc scale out.

### 4.5. Change and release support

1. Review thay đổi schema.
2. Review data migration script.
3. Review SQL trong release.
4. Hỗ trợ triển khai database change.
5. Kiểm tra sau release.
6. Rollback database change.

### 4.6. Compliance and audit

1. Review quyền định kỳ.
2. Xuất audit log.
3. Kiểm tra data masking.
4. Kiểm tra encryption.
5. Hỗ trợ audit nội bộ hoặc bên ngoài.

### 4.7. Security and key management

1. Certificate rotation.
2. Key rotation.
3. Secret rotation cho service account.
4. Review encryption compliance.

## 5. Danh mục dịch vụ lõi

### SR-DBA-001: Tạo database mới

1. Input bắt buộc:
   1. Tên ứng dụng.
   2. Môi trường.
   3. DBMS mục tiêu.
   4. Owner.
   5. Dung lượng dự kiến.
   6. Yêu cầu backup.
   7. Yêu cầu HA.
   8. Phân loại dữ liệu.
2. Output:
   1. Database được tạo theo naming convention.
   2. Owner và role cơ bản được cấu hình.
   3. Backup policy được áp dụng.
   4. Monitoring và alerting được đăng ký.
3. SLA đề xuất: 2 ngày làm việc nếu thông tin đầy đủ.
4. OLA đề xuất: Infra cung cấp VM hoặc resource trong 1 ngày nếu cần.
5. SOP liên quan:
   1. SOP Database Provisioning.
   2. SOP User and Permission Management.
6. Runbook liên quan:
   1. Runbook Database Provisioning.
7. Evidence bắt buộc:
   1. Ticket ID.
   2. Script hoặc pipeline output.
   3. Kết quả kiểm tra kết nối.
   4. Kết quả kiểm tra backup và monitoring.

### SR-DBA-002: Cấp quyền database

1. Input bắt buộc:
   1. User hoặc service account.
   2. Database.
   3. Môi trường.
   4. Quyền cần cấp.
   5. Lý do nghiệp vụ.
   6. Thời hạn quyền nếu là quyền tạm thời.
   7. Người phê duyệt.
2. Output:
   1. Quyền được cấp đúng phạm vi.
   2. Evidence được lưu.
3. SLA đề xuất: 1 ngày làm việc.
4. OLA đề xuất: Security approve trong 4 giờ nếu liên quan production.
5. SOP liên quan:
   1. SOP User and Permission Management.
6. Runbook liên quan:
   1. Runbook User Provisioning.
7. Evidence bắt buộc:
   1. Approval.
   2. Lệnh đã chạy.
   3. Kết quả kiểm tra quyền sau cấp.

### SR-DBA-003: Restore dữ liệu

1. Input bắt buộc:
   1. Database nguồn.
   2. Database đích.
   3. Recovery point.
   4. Lý do restore.
   5. Môi trường restore.
   6. RTO mong muốn.
   7. Người xác nhận dữ liệu sau restore.
2. Output:
   1. Database được restore.
   2. Kết quả validation.
   3. Evidence hoàn tất.
3. SLA đề xuất: Theo mức độ ưu tiên và kích thước dữ liệu.
4. SOP liên quan:
   1. SOP Restore Operation.
   2. SOP Disaster Recovery Drill nếu là diễn tập DR.
5. Runbook liên quan:
   1. Runbook Restore Validation.
6. Evidence bắt buộc:
   1. Restore log.
   2. Recovery point.
   3. Thời gian bắt đầu và kết thúc.
   4. Kết quả validation.

### SR-DBA-004: Query tuning support

1. Input bắt buộc:
   1. Query hoặc query ID.
   2. Thời gian xảy ra chậm.
   3. Môi trường.
   4. Mức ảnh hưởng.
   5. Execution plan nếu có.
   6. Baseline thời gian chạy kỳ vọng.
2. Output:
   1. Phân tích nguyên nhân.
   2. Đề xuất index hoặc rewrite.
   3. Evidence trước và sau nếu có thay đổi.
3. SLA đề xuất: Theo severity.
4. SOP liên quan:
   1. SOP Performance Troubleshooting.
   2. SOP Query Tuning Review.
5. Evidence bắt buộc:
   1. Query plan.
   2. Runtime metric.
   3. Wait hoặc lock evidence nếu liên quan.
   4. Kết quả thử nghiệm.

### SR-DBA-005: Failover hoặc switchover

1. Input bắt buộc:
   1. Hệ thống.
   2. Database hoặc cluster.
   3. Lý do chuyển đổi.
   4. Loại chuyển đổi.
   5. Maintenance window nếu có kế hoạch.
   6. Người phê duyệt.
   7. Application owner xác nhận.
2. Output:
   1. Primary mới hoạt động.
   2. Application kết nối thành công.
   3. Replication được tái lập hoặc xác nhận trạng thái.
   4. Evidence hoàn tất.
3. SLA đề xuất: Theo HA DR policy (DBA-POL-007).
4. SOP liên quan:
   1. SOP HA Failover and Switchover.
   2. SOP Replication Troubleshooting.
5. Runbook liên quan:
   1. Runbook Failover Execution.
6. Evidence bắt buộc:
   1. Trạng thái trước failover.
   2. Log thao tác.
   3. Trạng thái sau failover.
   4. Kết quả kiểm tra ứng dụng.

### SR-DBA-006: Review schema change

1. Input bắt buộc:
   1. DDL script.
   2. Data migration script nếu có.
   3. Môi trường.
   4. Dung lượng bảng ảnh hưởng.
   5. Rollback script.
   6. Kế hoạch triển khai.
2. Output:
   1. Kết quả review.
   2. Danh sách rủi ro.
   3. Điều kiện approve hoặc reject.
3. SLA đề xuất: 2 ngày làm việc.
4. SOP liên quan:
   1. SOP Schema Change Review.
5. Evidence bắt buộc:
   1. Review comment.
   2. Plan kiểm tra.
   3. Rủi ro locking hoặc downtime.
   4. Kết luận DBA.

## 6. Mức ưu tiên ticket đề xuất

1. P1: Dịch vụ production ngừng hoạt động hoặc mất dữ liệu đang xảy ra.
2. P2: Production suy giảm nghiêm trọng, có nguy cơ mất dữ liệu hoặc failover không ổn định.
3. P3: Lỗi vận hành ảnh hưởng một phần hoặc có workaround.
4. P4: Service request thông thường.
5. P5: Tư vấn, review, báo cáo hoặc yêu cầu không gấp.

## 7. Quy tắc cập nhật catalog

1. Thêm dịch vụ mới khi phát sinh cùng loại ít nhất 3 lần hoặc có nhu cầu vận hành định kỳ.
2. Cập nhật SLA khi dữ liệu vận hành cho thấy SLA hiện tại không thực tế.
3. Gắn SOP sau khi dịch vụ được chuẩn hóa.
4. Gắn runbook sau khi dịch vụ được tự động hóa.
5. Archive dịch vụ không còn sử dụng sau khi được owner phê duyệt.

## 8. Chỉ số đánh giá

1. Tỷ lệ ticket được phân loại đúng service.
2. Tỷ lệ service có SOP.
3. Tỷ lệ service có runbook.
4. Tỷ lệ service hoàn thành trong SLA.
5. Số lượng service request bị trả lại do thiếu input.
6. Số lỗi vận hành theo từng service type.
7. OLA compliance rate.

## 9. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-OF-001 | DBA Operations Framework Overview | Tổng quan framework |
| DBA-OM-002 | DBA Operating Model | Luồng xử lý ticket |
| DBA-OM-003 | DBA RACI Matrix | Trách nhiệm theo dịch vụ |
| DBA-POL-005 | Backup and Restore Policy | SLA backup restore |
| DBA-POL-007 | Database HA and DR Policy | SLA failover DR |
