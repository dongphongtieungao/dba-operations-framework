---
id: t-sql-query-performance-patterns
title: "T-SQL query performance patterns"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/logical-query-processing]]
  - [[concepts/sql-server-performance-diagnostics]]
confidence: unverified
---

## Definition

T-SQL query performance patterns là các thói quen viết truy vấn ảnh hưởng đến khả năng SQL Server dùng index, tối ưu plan và tránh xử lý thừa. Trong T-SQL Fundamentals, ví dụ nổi bật là lọc date range bằng khoảng nửa mở thay vì bọc cột trong YEAR/MONTH, thử nhiều cách giải cho cùng bài toán và dùng window functions/table expressions khi chúng làm truy vấn rõ hơn hoặc tối ưu hơn.

## Variants

- Sargable predicates: tránh thao tác trực tiếp lên cột lọc khi muốn tận dụng index.
- Deterministic ordering: dùng ORDER BY đủ khóa khi TOP/OFFSET hoặc previous/next logic phụ thuộc thứ tự.
- Rewrite patterns: thay subquery phức tạp bằng window functions hoặc table expressions khi phù hợp.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/sql-server-index-and-statistics-maintenance]]
- [[concepts/sql-server-plan-cache-analysis]]

## Mentioned In

## Notes

