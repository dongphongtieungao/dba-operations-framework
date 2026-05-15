---
id: t-sql-subqueries
title: "T-SQL subqueries"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/null-and-three-valued-logic]]
  - [[concepts/t-sql-table-expressions]]
confidence: unverified
---

## Definition

T-SQL subqueries là truy vấn lồng trong một truy vấn khác, có thể độc lập hoặc tương quan với truy vấn bên ngoài. Chúng hữu ích cho scalar lookup, multivalued filter, EXISTS/NOT EXISTS, previous/next value và running aggregate, nhưng dễ lỗi khi gặp NULL hoặc khi tên cột trong subquery không được prefix rõ ràng.

## Variants

- Self-contained subquery: không phụ thuộc truy vấn ngoài.
- Correlated subquery: tham chiếu cột từ truy vấn ngoài.
- Scalar subquery: trả về một giá trị.
- Multivalued subquery: trả về nhiều giá trị cho IN, ANY/SOME, ALL hoặc EXISTS.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/t-sql-window-functions]]
- [[concepts/t-sql-query-performance-patterns]]

## Mentioned In

## Notes

