---
id: runbook-database-provisioning
title: Runbook Database Provisioning
type: source
created: 2026-05-18
updated: 2026-05-18
authors:
  - DBA Team
year: 2026
importance: 3
urls: []
raw_paths:
  - raw/sources/dba_framework/document_guide/36_runbook_database_provisioning.md
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: skipped
findings: []
---

## Summary

Runbook Database Provisioning chuẩn hóa việc tạo database mới bằng script hoặc playbook, từ kiểm tra input, naming convention, owner, sizing, backup policy, monitoring, alerting đến cập nhật inventory và lưu evidence. Tài liệu đặt database provisioning vào lớp tự động hóa có kiểm soát: không chỉ tạo database, mà phải gắn quyền, backup, mã hóa, giám sát và tiêu chí bàn giao. Điểm cần hoàn thiện tiếp là bổ sung mẫu tham số cụ thể theo từng DBMS để có thể triển khai trực tiếp.

## Key Claims

- Database mới chỉ nên được tạo khi ticket, environment, dbms_type, database_name, owner, sizing, RPO/RTO, security class và retention đã rõ. (confidence: high)
- Provisioning phải tạo database đúng naming convention, cấu hình owner/role cơ bản, gán backup policy, bật monitoring/alert và cập nhật inventory. (confidence: high)
- Runbook provisioning cần idempotent ở mức có thể, validate input, tránh hard-code secret và xuất output có cấu trúc để lưu evidence. (confidence: high)
- Rollback phải dọn quyền, monitoring object và trạng thái CMDB nếu provisioning thất bại trước bàn giao. (confidence: high)
- Baseline hiện còn chung cho đa DBMS; các thao tác riêng của SQL Server, Azure SQL, PostgreSQL, MySQL/MariaDB, Oracle hoặc Z DB cần được đưa vào phụ lục vận hành. (confidence: medium)

## Evidence

- Input bắt buộc gồm ticket_id, environment, dbms_type, database_name, owner, sizing, rpo_rto, security_class và retention.
- Output bắt buộc yêu cầu database đúng naming convention, owner/role cơ bản, backup policy, monitoring/alert, inventory/CMDB và evidence trong ticket.
- Luồng tự động hóa gồm validate input/approval, kiểm tra tên database chưa tồn tại, tạo database, cấu hình parameter, tạo role/service account nếu cần, gán backup/retention/encryption, đăng ký monitoring, post-check và cập nhật ticket.
- Post-check yêu cầu database online, naming đúng, owner đúng, quyền tối thiểu, backup policy, monitoring và inventory.

## Related Concepts

- [[concepts/database-provisioning-runbook]]
- [[concepts/dba-automation-lifecycle]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-policy-sop-runbook-layering]]
- [[concepts/sql-server-configuration-baseline]]
- [[concepts/sql-server-backup-restore-operations]]
- [[concepts/azure-sql-database]]

## Related Sources

- [[sources/dba-operations-framework]]
- [[sources/runbook-user-provisioning]]
- [[sources/runbook-database-health-check]]

## People

Không có cá nhân cụ thể được tạo hồ sơ trong lần nạp này.

## Open Questions

- Cần bổ sung parameter profile chuẩn cho từng môi trường và từng DBMS.
- Cần xác định cơ chế kiểm tra tên database và cập nhật inventory/CMDB đang dùng trong thực tế.
- Cần nối runbook này với template service request để input không bị nhập tự do.

## Notes

