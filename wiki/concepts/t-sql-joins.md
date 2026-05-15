---
id: t-sql-joins
title: "T-SQL joins"
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

T-SQL joins là nhóm table operators dùng để kết hợp hàng từ nhiều bảng hoặc nhiều biểu thức bảng. Hiểu đúng cross join, inner join, outer join, self join, composite join và non-equi join giúp DBA đọc workload, phát hiện lỗi nhân dòng và giải thích vì sao bộ lọc ở ON hoặc WHERE có thể cho kết quả khác nhau.

## Variants

- Cross join: tạo tích Descartes, thường dùng có kiểm soát.
- Inner join: giữ các cặp khớp theo predicate.
- Outer join: giữ phía được bảo toàn và điền NULL cho phía thiếu.
- Multi-join query: nhiều phép join liên tiếp, nhạy cảm với logic ON/WHERE.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/null-and-three-valued-logic]]
- [[concepts/t-sql-query-performance-patterns]]

## Mentioned In

## Notes

