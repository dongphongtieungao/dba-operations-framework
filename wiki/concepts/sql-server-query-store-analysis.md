---
id: sql-server-query-store-analysis
title: "SQL Server Query Store analysis"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/master-dba-runbook-backend-java-azure-sql]]
  - [[sources/sql-server-dba-toolbox]]
related_concepts:
  - [[concepts/sql-server-performance-diagnostics]]
  - [[concepts/sql-server-index-and-statistics-maintenance]]
confidence: unverified
---

## Definition

SQL Server Query Store analysis là cách dùng Query Store để truy tìm truy vấn tốn logical reads, thời gian chạy dài, nhiều plan, key lookup, index usage hoặc chuỗi text cụ thể trong workload đã được ghi nhận.

## Variants

- Tìm truy vấn tốn tài nguyên nhất.
- So sánh nhiều plan của cùng truy vấn.
- Điều tra index usage và key lookup.

## Key Sources

- [[sources/master-dba-runbook-backend-java-azure-sql]]
- [[sources/sql-server-dba-toolbox]]

## Related Concepts

- [[concepts/sql-server-plan-cache-analysis]]
- [[concepts/sql-server-configuration-baseline]]

## Mentioned In

## Notes
