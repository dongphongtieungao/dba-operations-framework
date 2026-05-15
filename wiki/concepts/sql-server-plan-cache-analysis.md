---
id: sql-server-plan-cache-analysis
title: "SQL Server plan cache analysis"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/sql-server-dba-toolbox]]
related_concepts:
  - [[concepts/sql-server-performance-diagnostics]]
  - [[concepts/sql-server-query-store-analysis]]
confidence: unverified
---

## Definition

SQL Server plan cache analysis là việc đọc plan cache để tìm truy vấn đắt, index hint, implicit conversion, index scan, key lookup, missing index, probe residual, parallelism và cảnh báo trong execution plan.

## Variants

- Phân tích truy vấn đắt theo cache hiện có.
- Tìm dấu hiệu làm giảm chất lượng kế hoạch thực thi.
- Điều tra nhu cầu index hoặc rủi ro parallelism.

## Key Sources

- [[sources/sql-server-dba-toolbox]]

## Related Concepts

- [[concepts/sql-server-index-and-statistics-maintenance]]
- [[concepts/sql-server-performance-diagnostics]]

## Mentioned In

## Notes

