---
id: t-sql-set-operators
title: "T-SQL set operators"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/relational-query-thinking]]
  - [[concepts/logical-query-processing]]
confidence: unverified
---

## Definition

T-SQL set operators là các toán tử kết hợp kết quả của nhiều truy vấn dựa trên logic tập hợp: UNION, UNION ALL, INTERSECT và EXCEPT. Chúng hữu ích khi so sánh hoặc hợp nhất tập kết quả, nhưng DBA cần chú ý DISTINCT mặc định, precedence và các biến thể ALL mà T-SQL không hỗ trợ trực tiếp cho mọi toán tử.

## Variants

- UNION ALL: hợp nhất và giữ trùng lặp.
- UNION: hợp nhất và loại trùng lặp.
- INTERSECT: giữ phần giao.
- EXCEPT: giữ phần có ở truy vấn trái nhưng không có ở truy vấn phải.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/t-sql-query-performance-patterns]]

## Mentioned In

## Notes

