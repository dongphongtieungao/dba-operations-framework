---
id: runbook-database-health-check
title: Runbook Database Health Check
type: source
created: 2026-05-18
updated: 2026-05-18
authors:
  - DBA Team
year: 2026
importance: 4
urls: []
raw_paths:
  - raw/sources/dba_framework/document_guide/42_runbook_database_health_check.md
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: skipped
findings: []
---

## Summary

Runbook Database Health Check mô tả một baseline kiểm tra sức khỏe định kỳ cho database estate. Tài liệu gom các tín hiệu vận hành tối thiểu như instance, disk, connection, backup, replication, error log, blocking và alert rồi chuẩn hóa kết quả thành PASS/WARN/FAIL. Bản tối ưu trong wiki làm rõ vai trò của health check như một nguồn evidence định kỳ, hỗ trợ phát hiện sớm rủi ro trước khi chúng trở thành incident.

## Key Claims

- Health check định kỳ nên tự động hóa việc thu thập metric, trạng thái instance, disk, connection, replication, backup, error log và alert. (confidence: high)
- Script health check phải tránh hard-code credential, validate input và xuất output có cấu trúc để lưu evidence hoặc tích hợp ITSM. (confidence: high)
- Các điều kiện fail trọng yếu gồm production instance không truy cập được, backup vượt ngưỡng RPO, replication lag vượt ngưỡng, disk critical, lỗi corruption và alert P1/P2 chưa được xử lý. (confidence: high)
- Health check có giá trị vận hành khi liên kết với threshold profile, inventory, ticket định kỳ và hành động khuyến nghị. (confidence: high)
- Nội dung hiện là baseline đa DBMS; các khác biệt SQL Server, Azure SQL, PostgreSQL, MySQL/MariaDB, Oracle hoặc Z DB cần nằm trong phụ lục riêng. (confidence: medium)

## Evidence

- Input bắt buộc gồm execution_id, environment, dbms_type, instance_list, output_format và threshold_profile.
- Nhóm kiểm tra tối thiểu bao phủ instance uptime/role, disk data/log/temp/backup, connection usage, backup age, replication lag, error log, blocking/deadlock và alert tồn đọng.
- Luồng thực thi bắt đầu từ inventory và threshold profile, kết nối bằng service account read-only, thu thập metric/log summary, chuẩn hóa PASS/WARN/FAIL, sinh báo cáo và gửi cảnh báo khi cần.
- Output báo cáo gồm checked_at, instance_name, dbms_type, overall_status, backup_status, replication_status, capacity_status, critical_findings và recommended_action.

## Related Concepts

- [[concepts/database-health-check-runbook]]
- [[concepts/azure-sql-monitoring-and-observability]]
- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/sql-server-blocking-and-deadlock-investigation]]
- [[concepts/sql-server-backup-restore-operations]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-policy-sop-runbook-layering]]

## Related Sources

- [[sources/dba-operations-framework]]
- [[sources/runbook-failover-execution]]
- [[sources/runbook-capacity-report]]

## People

Không có cá nhân cụ thể được tạo hồ sơ trong lần nạp này.

## Open Questions

- Cần bổ sung ngưỡng mặc định cho từng môi trường để runbook có thể chạy mà không phụ thuộc hoàn toàn vào profile bên ngoài.
- Cần tách danh sách query hoặc API call theo từng DBMS để health check có thể triển khai ngay.
- Cần xác định rõ cách gom nhiều instance thành một báo cáo estate-level thay vì chỉ báo cáo từng instance.

## Notes
