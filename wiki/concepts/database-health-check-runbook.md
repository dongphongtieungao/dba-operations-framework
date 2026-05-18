---
id: database-health-check-runbook
title: Database Health Check Runbook
type: concept
created: 2026-05-18
updated: 2026-05-18
key_sources:
  - [[sources/runbook-database-health-check]]
related_concepts:
  - [[concepts/azure-sql-monitoring-and-observability]]
  - [[concepts/sql-server-performance-diagnostics]]
  - [[concepts/sql-server-backup-restore-operations]]
  - [[concepts/dba-evidence-driven-operations]]
confidence: unverified
---

## Definition

Database Health Check Runbook là baseline kiểm tra định kỳ giúp DBA xác định trạng thái sức khỏe của database estate bằng các tín hiệu như instance, disk, connection, backup, replication, error log, blocking, deadlock và alert. Kết quả thường được chuẩn hóa thành PASS, WARN hoặc FAIL để tạo báo cáo, cảnh báo hoặc ticket xử lý.

## Variants

- Daily health check: kiểm tra nhanh các ngưỡng vận hành quan trọng mỗi ngày.
- Pre-change health check: xác nhận hệ thống đủ ổn định trước khi thay đổi.
- Post-incident health check: kiểm tra dấu hiệu còn sót sau sự cố.
- Estate-level health check: gom nhiều instance/database thành một báo cáo tổng quan.

## Key Sources

- [[sources/runbook-database-health-check]]

## Related Concepts

- [[concepts/azure-sql-monitoring-and-observability]]
- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/sql-server-blocking-and-deadlock-investigation]]
- [[concepts/sql-server-backup-restore-operations]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-policy-sop-runbook-layering]]

## Mentioned In

## Notes

