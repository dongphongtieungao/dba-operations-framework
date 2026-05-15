---
id: sql-server-index-and-statistics-maintenance
title: "SQL Server index and statistics maintenance"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/master-dba-runbook-backend-java-azure-sql]]
  - [[sources/sql-server-2022-administration-inside-out]]
  - [[sources/sql-server-dba-toolbox]]
related_concepts:
  - [[concepts/sql-server-performance-diagnostics]]
  - [[concepts/sql-server-query-store-analysis]]
confidence: unverified
---

## Definition

SQL Server index and statistics maintenance là nhóm tác vụ tìm missing/unused/duplicate indexes, fragmentation, fillfactor lệch chuẩn, overlapping statistics và cơ hội dùng clustered columnstore để cải thiện hiệu năng hoặc giảm lãng phí.

## Variants

- Tìm index thiếu từ DMV, plan cache hoặc Query Store.
- Tìm index dư, trùng hoặc không dùng.
- Kiểm tra statistics và fragmentation.

## Key Sources

- [[sources/master-dba-runbook-backend-java-azure-sql]]
- [[sources/sql-server-2022-administration-inside-out]]
- [[sources/sql-server-dba-toolbox]]

## Related Concepts

- [[concepts/sql-server-plan-cache-analysis]]
- [[concepts/sql-server-storage-and-tempdb-operations]]

## Mentioned In

## Notes
