---
doc_id: DBA-RBK-038
title: "Runbook Backup Verification"
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
  - DBA-POL-005 Backup and Restore Policy
  - DBA-SOP-023 SOP Backup Operation
  - DBA-TMP-048 Template Operation Evidence
---

# DBA-RBK-038 - Runbook Backup Verification

## 1. Mục đích

Tự động hóa việc kiểm tra trạng thái backup nhằm phát hiện sớm rủi ro mất khả năng phục hồi dữ liệu.

## 2. Mô tả tóm tắt

Runbook này mô tả cách thiết kế, vận hành và kiểm soát script kiểm tra backup status, backup age, backup chain, dung lượng backup, lỗi backup gần nhất và sinh báo cáo daily.

## 3. Phạm vi áp dụng

Runbook áp dụng cho các hệ thống database production, staging quan trọng và các môi trường có yêu cầu kiểm soát backup định kỳ.

Áp dụng cho các nhóm DBMS sau:

| Nhóm DBMS | Phạm vi kiểm tra tối thiểu |
|---|---|
| SQL Server | Full backup, differential backup, transaction log backup, backup job status, backup chain |
| PostgreSQL | Base backup, WAL archive, backup age, WAL continuity, backup manifest nếu có |
| MySQL/MariaDB | Physical backup hoặc logical backup, binlog availability, backup status, GTID/binlog position |
| Oracle | RMAN backup status, archive log backup, backup pieces, recoverability |
| Azure SQL | Built-in backup, PITR setting, long-term retention nếu có, geo restore readiness |
| Z DB | Backup job status, backup age, restore point, HA/DR backup policy theo nền tảng |

## 4. Nguyên tắc kiểm soát

1. Không được chỉ kiểm tra backup job success. Phải kiểm tra khả năng phục hồi thực tế ở mức tối thiểu thông qua backup age, backup chain và restore point.
2. Backup hợp lệ phải đáp ứng RPO, retention, mã hóa, vị trí lưu trữ và khả năng truy xuất.
3. Mọi cảnh báo backup failed, backup overdue hoặc backup chain broken phải tạo ticket hoặc alert theo quy định.
4. Kết quả kiểm tra backup phải có evidence và có thể đối chiếu với policy.
5. Không chạy script kiểm tra bằng tài khoản cá nhân trên production. Nên dùng service account có quyền đọc metadata backup.

## 5. Điều kiện tiên quyết

| Hạng mục | Yêu cầu |
|---|---|
| Inventory database | Có danh sách instance, database, environment, owner, RPO, RTO |
| Credential | Có service account chỉ đọc metadata backup |
| Policy | Có Backup and Restore Policy đã được phê duyệt |
| Tooling | Có nơi lưu output, ví dụ file share, object storage, GitLab artifact, ServiceNow attachment hoặc monitoring system |
| Monitoring | Có dashboard hoặc alert channel để gửi kết quả bất thường |
| Time sync | Máy chạy script phải đồng bộ NTP để tính backup age chính xác |

## 6. Input bắt buộc

| Trường | Bắt buộc | Mô tả |
|---|---:|---|
| ticket_id | Không | Ticket nếu chạy theo yêu cầu ad hoc |
| execution_id | Có | Mã lần chạy script |
| environment | Có | Production, staging, development |
| dbms_type | Có | SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL, Z DB |
| instance_name | Có | Tên instance hoặc endpoint |
| database_name | Có | Tên database |
| rpo_minutes | Có | RPO theo policy hoặc service catalog |
| retention_days | Có | Số ngày retention cần kiểm tra |
| report_date | Có | Ngày sinh báo cáo |

## 7. Output bắt buộc

Output nên dùng định dạng JSON, CSV hoặc Markdown. Trường tối thiểu:

| Trường | Mô tả |
|---|---|
| execution_id | Mã lần chạy |
| checked_at | Thời điểm kiểm tra |
| dbms_type | Loại DBMS |
| instance_name | Instance hoặc server |
| database_name | Database |
| last_successful_backup_time | Thời điểm backup thành công gần nhất |
| backup_age_minutes | Số phút kể từ backup thành công gần nhất |
| backup_type | Full, differential, log, WAL, RMAN, built-in |
| backup_chain_status | OK, Broken, Unknown, Not applicable |
| retention_status | OK, Violation, Unknown |
| encryption_status | OK, Violation, Unknown |
| storage_status | OK, Warning, Critical |
| result | PASS, WARN, FAIL |
| evidence_path | Đường dẫn evidence |
| recommended_action | Hành động khuyến nghị |

## 8. Quy trình thực thi

### 8.1. Chuẩn bị

1. Xác nhận danh sách database cần kiểm tra từ inventory.
2. Xác nhận RPO, RTO và retention theo policy.
3. Xác nhận tài khoản chạy script có quyền đọc metadata backup.
4. Xác nhận nơi lưu báo cáo và evidence.
5. Xác nhận kênh cảnh báo khi phát hiện kết quả FAIL.

### 8.2. Kiểm tra metadata backup

Script cần thực hiện các kiểm tra tối thiểu sau:

| Kiểm tra | Mục tiêu |
|---|---|
| Backup job status | Xác định job gần nhất có lỗi hay không |
| Last successful backup | Xác định backup thành công gần nhất |
| Backup age | So sánh với RPO hoặc backup schedule |
| Backup chain | Phát hiện mất chain đối với log/WAL/binlog |
| Backup file availability | Kiểm tra file backup còn tồn tại và truy cập được |
| Backup size trend | Phát hiện backup bất thường quá nhỏ hoặc tăng đột biến |
| Encryption | Kiểm tra backup có được mã hóa nếu policy yêu cầu |
| Retention | Kiểm tra backup có đủ số ngày lưu trữ |
| Storage capacity | Kiểm tra dung lượng nơi lưu backup |
| Error log | Thu thập lỗi backup gần nhất |

### 8.3. Đánh giá kết quả

| Điều kiện | Kết quả | Hành động |
|---|---|---|
| Backup thành công, backup age trong ngưỡng, chain hợp lệ | PASS | Ghi evidence |
| Backup thành công nhưng gần vượt ngưỡng RPO hoặc dung lượng thấp | WARN | Ghi evidence và tạo warning |
| Không có backup hợp lệ trong ngưỡng RPO | FAIL | Tạo incident hoặc ticket xử lý |
| Backup chain broken | FAIL | Escalate DBA Lead |
| Backup file không truy cập được | FAIL | Escalate Storage hoặc Infra |
| Không xác định được trạng thái backup | WARN hoặc FAIL | Điều tra credential, metadata, monitoring |

### 8.4. Sinh báo cáo daily

Báo cáo daily nên gồm:

1. Tổng số database đã kiểm tra.
2. Số lượng PASS, WARN, FAIL.
3. Danh sách database FAIL.
4. Danh sách database có backup overdue.
5. Danh sách database có backup chain broken.
6. Dung lượng backup hiện tại và cảnh báo capacity.
7. Action item và owner.

## 9. Pseudocode tham khảo

```text
load inventory
for each database in inventory:
    read backup policy
    collect backup metadata
    calculate backup_age_minutes
    validate backup_chain
    validate backup_file_access
    validate retention
    validate encryption
    validate storage_capacity
    classify result as PASS/WARN/FAIL
    write evidence record

generate daily report
send alert for FAIL
attach report to ticket or evidence repository
```

## 10. Ví dụ logic kiểm tra trạng thái

```text
IF last_successful_backup_time IS NULL
    result = FAIL
    recommended_action = "No successful backup found. Escalate immediately."

ELSE IF backup_age_minutes > rpo_minutes
    result = FAIL
    recommended_action = "Backup age exceeds RPO. Check backup job and run ad hoc backup if approved."

ELSE IF backup_chain_status = "Broken"
    result = FAIL
    recommended_action = "Backup chain is broken. Validate recoverability and consider full backup."

ELSE IF storage_free_percent < threshold
    result = WARN
    recommended_action = "Backup storage is near capacity. Expand or purge according to retention policy."

ELSE
    result = PASS
```

## 11. Gợi ý kiểm tra theo DBMS

### 11.1. SQL Server

Kiểm tra tối thiểu:

1. `msdb.dbo.backupset`
2. `msdb.dbo.backupmediafamily`
3. SQL Agent job history
4. Full, differential và log backup sequence
5. Recovery model của database
6. Backup compression và encryption nếu có

### 11.2. PostgreSQL

Kiểm tra tối thiểu:

1. Base backup gần nhất
2. WAL archive continuity
3. Replication slot nếu liên quan
4. Lỗi archive command
5. Backup manifest nếu giải pháp backup hỗ trợ

### 11.3. MySQL hoặc MariaDB

Kiểm tra tối thiểu:

1. Backup job log
2. Binlog enabled
3. GTID hoặc binlog position
4. Backup file size
5. Replica backup consistency nếu backup từ replica

### 11.4. Oracle

Kiểm tra tối thiểu:

1. RMAN backup summary
2. Archive log backup
3. Backup pieces availability
4. Obsolete backup theo retention
5. Recoverability theo RMAN catalog hoặc control file

### 11.5. Azure SQL

Kiểm tra tối thiểu:

1. PITR setting
2. Long term retention nếu được cấu hình
3. Geo restore hoặc failover group nếu có
4. Azure Monitor alert cho backup hoặc restore capability
5. Policy compliance theo subscription hoặc resource group

## 12. Điều kiện tạo ticket hoặc incident

| Điều kiện | Mức xử lý |
|---|---|
| Không có backup hợp lệ cho production trong ngưỡng RPO | Incident hoặc ticket P1/P2 theo criticality |
| Backup chain broken | Escalate DBA Lead và tạo ticket xử lý ngay |
| Backup file không truy cập được | Escalate Infra/Storage/Cloud Team |
| Backup storage còn thấp hơn ngưỡng critical | Ticket capacity hoặc storage |
| Backup encryption/retention không đúng policy | Ticket compliance hoặc security |
| Không kiểm tra được backup do lỗi credential/metadata | Ticket vận hành để khôi phục khả năng kiểm chứng |

## 13. Cổng kiểm soát tự động hóa

1. Script phải trả exit code hoặc final result rõ ràng: PASS, WARN hoặc FAIL.
2. Mọi FAIL phải có recommended_action và owner mặc định.
3. Script không được xóa hoặc thay đổi backup trong quá trình verification.
4. Raw output không được chứa secret, password hoặc dữ liệu nhạy cảm.
5. Nếu chạy theo lịch, job phải có alert khi không chạy hoặc chạy quá lâu.
6. Báo cáo daily phải có tổng số database trong inventory và số database đã kiểm tra để đo coverage.

## 14. Evidence bắt buộc

| Evidence | Bắt buộc | Ghi chú |
|---|---:|---|
| Execution log | Có | Log chạy script |
| Backup verification report | Có | Báo cáo daily |
| Raw query output | Có | Output metadata backup |
| Alert evidence | Có nếu FAIL | Link alert hoặc incident |
| Ticket ID | Có nếu phát sinh xử lý | ServiceNow hoặc ITSM |
| Screenshot dashboard | Tùy chọn | Khi cần gửi khách hàng |
| Action item | Có nếu WARN/FAIL | Owner và deadline |

## 15. Escalation

| Tình huống | Escalation |
|---|---|
| Không có backup hợp lệ cho production | DBA Lead, Service Owner |
| Backup chain broken | DBA Lead, Application Owner |
| Backup storage full | Infra hoặc Storage Team |
| Backup encryption violation | Security Team |
| Không truy cập được backup repository | Infra, Storage, Cloud Team |
| Lỗi lặp lại hơn 2 lần | Problem Management hoặc RCA |

## 16. Tiêu chí hoàn tất

Runbook được coi là hoàn tất khi:

1. Tất cả database trong scope đã được kiểm tra.
2. Kết quả PASS/WARN/FAIL được ghi rõ.
3. Báo cáo daily được sinh.
4. Mọi FAIL đã có ticket hoặc incident.
5. Evidence đã được lưu vào repository.
6. Action item có owner và deadline.

## 17. Chỉ số liên quan

| KPI | Công thức tham khảo |
|---|---|
| Backup success rate | Successful backup checks / Total backup checks |
| Backup compliance rate | Databases meeting RPO / Total databases |
| Backup verification coverage | Databases checked / Databases in inventory |
| Backup chain integrity rate | Valid chains / Total chain checks |
| Backup incident count | Number of backup related incidents |
