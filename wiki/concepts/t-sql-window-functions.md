---
id: t-sql-window-functions
title: "T-SQL window functions"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/logical-query-processing]]
  - [[concepts/t-sql-subqueries]]
confidence: unverified
---

## Definition

T-SQL window functions là các hàm tính toán trên một cửa sổ hàng liên quan tới hàng hiện tại, được mô tả bằng mệnh đề OVER. Chúng đặc biệt quan trọng cho phân tích dữ liệu vì giải quyết gọn các bài toán ranking, previous/next value, running totals, moving aggregates và phân tích theo partition mà không làm mất chi tiết từng dòng như GROUP BY.

## Variants

- Ranking functions: ROW_NUMBER, RANK, DENSE_RANK, NTILE.
- Offset functions: LAG, LEAD, FIRST_VALUE, LAST_VALUE.
- Aggregate window functions: SUM, AVG, MIN, MAX, COUNT với OVER và frame.
- Time-series patterns: bucket, gap filling và running aggregate.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/t-sql-query-performance-patterns]]
- [[concepts/t-sql-table-expressions]]

## Mentioned In

## Notes

