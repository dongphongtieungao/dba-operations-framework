---
id: sql-server-performance-diagnostics
title: "SQL Server performance diagnostics"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/master-dba-runbook-backend-java-azure-sql]]
  - [[sources/sql-server-2022-administration-inside-out]]
  - [[sources/sql-server-dba-toolbox]]
related_concepts:
  - [[concepts/sql-server-query-store-analysis]]
  - [[concepts/sql-server-plan-cache-analysis]]
confidence: unverified
---

## Definition

SQL Server performance diagnostics là nhóm kỹ thuật tìm nguyên nhân workload chậm hoặc tốn tài nguyên bằng cách xem wait statistics, buffer cache, truy vấn đắt, stored procedure chậm, hoạt động hiện tại, plan cache và Query Store.

## Variants

- Chẩn đoán thời điểm hiện tại: dùng công cụ kiểu sp_WhoIsActive, locks, open transactions.
- Chẩn đoán lịch sử: dùng Query Store, plan cache hoặc thống kê chờ.
- Chẩn đoán theo tài nguyên: IO, CPU, memory, row throughput và buffer cache.

## Key Sources

- [[sources/master-dba-runbook-backend-java-azure-sql]]
- [[sources/sql-server-2022-administration-inside-out]]
- [[sources/sql-server-dba-toolbox]]

## Related Concepts

- [[concepts/sql-server-extended-events-monitoring]]
- [[concepts/sql-server-index-and-statistics-maintenance]]

## Mentioned In

## Notes
