---
id: sql-server-configuration-baseline
title: "SQL Server configuration baseline"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/sql-server-2022-administration-inside-out]]
  - [[sources/sql-server-dba-toolbox]]
  - [[sources/runbook-database-provisioning]]
related_concepts:
  - [[concepts/sql-server-dba-toolbox]]
  - [[concepts/sql-server-query-store-analysis]]
  - [[concepts/database-provisioning-runbook]]
confidence: unverified
---

## Definition

SQL Server configuration baseline là tập các cấu hình instance/database được kiểm tra hoặc chuẩn hóa để giảm rủi ro vận hành, gồm compatibility level, auto-growth, page verification, recovery model, database owner, statistics, Query Store và các tùy chọn cấp instance.

## Variants

- Baseline kiểm tra: tìm cấu hình lệch chuẩn hoặc không mong muốn.
- Baseline chỉnh sửa: sinh hoặc chạy lệnh để đưa cấu hình về chuẩn mong muốn.
- Baseline hàng loạt: áp dụng cho nhiều database hoặc nhiều instance qua Central Management Server.

## Key Sources

- [[sources/sql-server-2022-administration-inside-out]]
- [[sources/sql-server-dba-toolbox]]
- [[sources/runbook-database-provisioning]]

## Related Concepts

- [[concepts/sql-server-agent-job-operations]]
- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/database-provisioning-runbook]]

## Mentioned In

## Notes
