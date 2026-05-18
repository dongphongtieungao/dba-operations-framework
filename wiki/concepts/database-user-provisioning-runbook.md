---
id: database-user-provisioning-runbook
title: Database User Provisioning Runbook
type: concept
created: 2026-05-18
updated: 2026-05-18
key_sources:
  - [[sources/runbook-user-provisioning]]
related_concepts:
  - [[concepts/sql-server-security-auditing]]
  - [[concepts/dba-evidence-driven-operations]]
  - [[concepts/dba-automation-lifecycle]]
confidence: unverified
---

## Definition

Database User Provisioning Runbook là quy trình tạo user, gán role hoặc permission, kiểm tra quyền và lưu evidence theo ticket. Nó kiểm soát truy cập bằng approval, role chuẩn, thời hạn quyền, audit trước/sau thay đổi và cơ chế thu hồi.

## Variants

- Human access: quyền cho người dùng, thường cần thời hạn hoặc review định kỳ.
- Service account access: quyền cho automation hoặc service, cần kiểm tra dependency.
- Application account access: quyền cho ứng dụng, cần quản trị thay đổi kỹ hơn.
- Temporary access: quyền có expiration date và bằng chứng thu hồi.

## Key Sources

- [[sources/runbook-user-provisioning]]

## Related Concepts

- [[concepts/sql-server-security-auditing]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-policy-sop-runbook-layering]]
- [[concepts/dba-automation-lifecycle]]

## Mentioned In

## Notes

