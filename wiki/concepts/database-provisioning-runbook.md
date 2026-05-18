---
id: database-provisioning-runbook
title: Database Provisioning Runbook
type: concept
created: 2026-05-18
updated: 2026-05-18
key_sources:
  - [[sources/runbook-database-provisioning]]
related_concepts:
  - [[concepts/dba-automation-lifecycle]]
  - [[concepts/dba-evidence-driven-operations]]
  - [[concepts/sql-server-configuration-baseline]]
confidence: unverified
---

## Definition

Database Provisioning Runbook là quy trình tạo database mới theo chuẩn vận hành, bao gồm validate yêu cầu, tạo database, cấu hình baseline, owner, role, backup, monitoring, alert, inventory và evidence. Mục tiêu là biến việc tạo database từ thao tác thủ công thành quy trình có kiểm soát, có thể lặp lại và có thể audit.

## Variants

- Production provisioning: cần approval, sizing, backup, retention, security class và monitoring đầy đủ.
- Non-production provisioning: có thể đơn giản hơn nhưng vẫn cần naming, owner, quyền và inventory.
- Platform API provisioning: gọi API của nền tảng thay vì chạy lệnh DBMS trực tiếp.
- Template-based provisioning: dùng profile chuẩn theo environment hoặc service tier.

## Key Sources

- [[sources/runbook-database-provisioning]]

## Related Concepts

- [[concepts/dba-automation-lifecycle]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-policy-sop-runbook-layering]]
- [[concepts/sql-server-configuration-baseline]]
- [[concepts/sql-server-backup-restore-operations]]
- [[concepts/azure-sql-database]]

## Mentioned In

## Notes

