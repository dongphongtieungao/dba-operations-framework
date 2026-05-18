---
doc_id: DBA-RBK-039
title: "Runbook Restore Validation"
doc_type: runbook
priority: 1
status: draft
owner: DBA Team
reviewer: DBA Lead
approver: Service Owner
version: 0.2
created_date: 2026-05-15
last_updated: 2026-05-18
related_documents:
  - DBA-POL-005 Backup and Restore Policy
  - DBA-SOP-024 SOP Restore Operation
  - DBA-SOP-029 SOP Disaster Recovery Drill
  - DBA-TMP-048 Template Operation Evidence
  - DBA-TMP-049 Template Restore Drill Report
---

# DBA-RBK-039 - Runbook Restore Validation

  ## 1. Mục đích

  Tự động hóa việc kiểm tra sau restore để xác nhận database đã được phục hồi đúng, đủ và sẵn sàng bàn giao.

  ## 2. Mô tả tóm tắt

  Runbook này mô tả cách thiết kế và vận hành script validation sau restore, bao gồm kiểm tra object count, row count, checksum nếu có, trạng thái database, trạng thái ứng dụng và xuất kết quả nghiệm thu.

  ## 3. Phạm vi áp dụng

Runbook áp dụng cho các hoạt động restore production, restore sang non-production, restore phục vụ DR drill, restore phục vụ kiểm thử dữ liệu, restore ad hoc theo yêu cầu nghiệp vụ.

## 4. Nguyên tắc kiểm soát

1. Restore chưa được xem là hoàn tất nếu chưa có validation.
2. Validation phải dựa trên tiêu chí đã thống nhất trước khi restore.
3. Không dùng cảm tính hoặc chỉ dựa vào việc database online để kết luận restore thành công.
4. Với dữ liệu nhạy cảm restore sang non-production, phải kiểm tra data masking trước khi bàn giao.
5. Kết quả validation phải được lưu dưới dạng evidence và liên kết với ticket hoặc báo cáo drill.

## 5. Điều kiện tiên quyết

| Hạng mục | Yêu cầu |
|---|---|
| Restore request | Có ticket hoặc yêu cầu được phê duyệt |
| Restore scope | Xác định database, schema, table hoặc thời điểm phục hồi |
| Recovery point | Xác định backup source hoặc PITR timestamp |
| Validation criteria | Xác định trước các query, object, row count, checksum hoặc business sample cần kiểm tra |
| Access | Service account có quyền đọc metadata và dữ liệu kiểm tra |
| Security | Nếu là non-production, phải có rule masking nếu dữ liệu nhạy cảm |
| Evidence path | Có nơi lưu output và báo cáo nghiệm thu |

## 6. Input bắt buộc

| Trường | Bắt buộc | Mô tả |
|---|---:|---|
| ticket_id | Có | Ticket yêu cầu restore |
| execution_id | Có | Mã lần chạy validation |
| source_database | Có | Database nguồn |
| target_database | Có | Database sau restore |
| restore_type | Có | Full, PITR, table-level, schema-level, DR restore |
| recovery_point | Có | Thời điểm hoặc backup ID |
| validation_profile | Có | Bộ rule validation cần chạy |
| requester | Có | Người yêu cầu hoặc owner |
| environment | Có | Production, staging, test, DR |

## 7. Output bắt buộc

| Trường | Mô tả |
|---|---|
| execution_id | Mã lần chạy |
| validated_at | Thời điểm kiểm tra |
| source_database | Database nguồn |
| target_database | Database sau restore |
| restore_type | Loại restore |
| recovery_point | Recovery point đã sử dụng |
| database_status | Online, restoring, suspect, read only hoặc trạng thái tương ứng |
| object_validation_result | PASS, WARN, FAIL |
| row_count_validation_result | PASS, WARN, FAIL |
| checksum_validation_result | PASS, WARN, FAIL, Not applicable |
| application_validation_result | PASS, WARN, FAIL, Not performed |
| masking_validation_result | PASS, WARN, FAIL, Not applicable |
| final_result | PASS, WARN, FAIL |
| evidence_path | Đường dẫn evidence |
| sign_off_required | Yes hoặc No |

## 8. Quy trình thực thi

### 8.1. Xác nhận thông tin restore

1. Đọc ticket hoặc yêu cầu restore.
2. Xác định mục đích restore.
3. Xác định recovery point.
4. Xác định target environment.
5. Xác định tiêu chí validation.
6. Xác định người xác nhận kết quả cuối cùng.

### 8.2. Kiểm tra trạng thái database

Kiểm tra tối thiểu:

| Kiểm tra | Mục tiêu |
|---|---|
| Database online | Xác nhận database có thể truy cập |
| Read/write mode | Xác nhận chế độ phù hợp với mục đích |
| Recovery state | Xác nhận quá trình recovery đã hoàn tất |
| Owner và permission | Xác nhận quyền truy cập phù hợp |
| Collation, charset, timezone | Xác nhận cấu hình không sai lệch nghiêm trọng |
| Orphan user | Phát hiện user bị lệch mapping sau restore |
| Job hoặc scheduler | Xác định job cần disable nếu restore sang non-production |

### 8.3. Kiểm tra metadata object

Kiểm tra các nhóm object:

1. Schema.
2. Table.
3. View.
4. Index.
5. Constraint.
6. Sequence hoặc identity.
7. Stored procedure, function, package.
8. Trigger.
9. Synonym hoặc external object nếu có.
10. Role và permission liên quan.

### 8.4. Kiểm tra dữ liệu

Tùy mục tiêu restore, chọn một hoặc nhiều kỹ thuật sau:

| Kỹ thuật | Khi dùng |
|---|---|
| Row count | Kiểm tra nhanh số lượng record |
| Table checksum | Kiểm tra tính toàn vẹn dữ liệu ở mức bảng |
| Sample query | Kiểm tra nghiệp vụ đại diện |
| Business key check | Kiểm tra dữ liệu theo khóa nghiệp vụ |
| Min/max timestamp | Kiểm tra dữ liệu có đúng recovery point không |
| Referential integrity check | Kiểm tra quan hệ dữ liệu |
| Application smoke test | Kiểm tra ứng dụng có đọc được dữ liệu không |

### 8.5. Kiểm tra bảo mật sau restore

1. Xác nhận không có quyền production không cần thiết trên target.
2. Xác nhận tài khoản cá nhân không được cấp quyền vượt chuẩn.
3. Xác nhận dữ liệu nhạy cảm đã masking nếu restore sang non-production.
4. Xác nhận audit log hoặc access log đã bật nếu policy yêu cầu.
5. Xác nhận connection string không trỏ nhầm production.

### 8.6. Sinh báo cáo nghiệm thu

Báo cáo cần gồm:

1. Ticket ID.
2. Database nguồn.
3. Database đích.
4. Thời điểm restore.
5. Recovery point.
6. Restore duration.
7. Validation duration.
8. Kết quả từng nhóm kiểm tra.
9. Kết quả cuối cùng.
10. Người xác nhận.
11. Action item nếu có lỗi.

## 9. Pseudocode tham khảo

```text
load restore request
load validation profile
connect target database

check database status
check object metadata
for each validation rule:
    execute validation query
    compare actual result with expected result
    classify PASS/WARN/FAIL

if target environment is non-production:
    run masking validation

generate validation report
store evidence
notify requester for sign-off
```

## 10. Ví dụ validation profile

```yaml
validation_profile_id: restore-validation-core
checks:
  - name: database_online
    type: metadata
    severity: critical
  - name: table_count_check
    type: metadata
    expected_minimum: 1
    severity: critical
  - name: critical_table_row_count
    type: row_count
    tables:
      - customer
      - orders
      - payment
    tolerance_percent: 0
    severity: critical
  - name: business_sample_check
    type: query
    query_file: validation/business_sample.sql
    severity: major
  - name: masking_check
    type: query
    query_file: validation/masking_check.sql
    applies_to:
      - test
      - staging
    severity: critical
```

## 11. Gợi ý kiểm tra theo DBMS

### 11.1. SQL Server

Kiểm tra tối thiểu:

1. Database state.
2. Recovery model.
3. DBCC CHECKDB nếu phạm vi cho phép.
4. Orphan users.
5. SQL Agent jobs cần disable nếu non-production.
6. Row count hoặc checksum cho bảng quan trọng.

### 11.2. PostgreSQL

Kiểm tra tối thiểu:

1. Database connection.
2. Schema và table count.
3. Sequence ownership và sequence last value.
4. Extension availability.
5. Constraint validation.
6. `amcheck` nếu phù hợp và đã được phê duyệt.

### 11.3. MySQL hoặc MariaDB

Kiểm tra tối thiểu:

1. Table status.
2. Engine consistency.
3. Row count cho bảng quan trọng.
4. Character set và collation.
5. Definer của view, procedure, trigger.
6. Event scheduler nếu có.

### 11.4. Oracle

Kiểm tra tối thiểu:

1. Database open mode.
2. Tablespace status.
3. Invalid objects.
4. User/schema status.
5. RMAN validate hoặc kiểm tra liên quan nếu cần.
6. Object count và sample business query.

### 11.5. Azure SQL

Kiểm tra tối thiểu:

1. Database status.
2. Restore timestamp.
3. Query Store status nếu cần.
4. User và role.
5. Firewall/private endpoint hoặc connectivity.
6. Application smoke test.

## 12. Phân loại kết quả

| Điều kiện | Kết quả | Hành động |
|---|---|---|
| Tất cả critical check PASS | PASS | Gửi requester xác nhận |
| Có warning nhưng không ảnh hưởng mục tiêu restore | WARN | Gửi requester kèm ghi chú |
| Có critical check FAIL | FAIL | Không bàn giao, mở ticket xử lý |
| Không chạy được validation do lỗi credential | FAIL | Điều tra quyền hoặc service account |
| Không đủ tiêu chí validation | WARN | Yêu cầu requester xác nhận giới hạn kiểm tra |

## 13. Evidence bắt buộc

| Evidence | Bắt buộc | Ghi chú |
|---|---:|---|
| Restore log | Có | Log quá trình restore |
| Validation report | Có | Báo cáo script sinh ra |
| Validation raw output | Có | Output query hoặc metadata |
| Recovery point evidence | Có | Backup ID hoặc PITR timestamp |
| Sign-off requester | Có | Email, ticket comment hoặc approval |
| Masking evidence | Có nếu non-production có dữ liệu nhạy cảm | Không lưu dữ liệu nhạy cảm thô trong evidence |
| Screenshot dashboard | Tùy chọn | Khi cần chứng minh trạng thái ứng dụng |

## 14. Escalation

| Tình huống | Escalation |
|---|---|
| Restore thành công nhưng validation critical fail | DBA Lead, Application Owner |
| Dữ liệu không đúng recovery point | DBA Lead, Service Owner |
| Dữ liệu nhạy cảm chưa được masking ở non-production | Security Team |
| Ứng dụng không kết nối được sau restore | Application Team, Infra Team |
| Object invalid nhiều sau restore | DBA Lead, Application Owner |
| Không thể xác nhận kết quả với requester | Service Owner |

## 15. Tiêu chí hoàn tất

Runbook được coi là hoàn tất khi:

1. Database sau restore truy cập được.
2. Validation profile đã chạy xong.
3. Critical check đạt PASS hoặc có quyết định chấp nhận rủi ro.
4. Evidence đã được lưu.
5. Requester hoặc Service Owner xác nhận kết quả.
6. Ticket được cập nhật kết quả cuối cùng.
7. Action item được tạo nếu có WARN hoặc FAIL.

## 16. Chỉ số liên quan

| KPI | Công thức tham khảo |
|---|---|
| Restore validation success rate | Successful validations / Total restore validations |
| Restore duration | Restore end time - Restore start time |
| Validation duration | Validation end time - Validation start time |
| RTO compliance rate | Restores meeting RTO / Total restores |
| RPO compliance rate | Restores meeting RPO / Total restores |
| Restore defect count | Number of validation failures |
