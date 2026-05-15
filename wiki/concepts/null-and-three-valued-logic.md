---
id: null-and-three-valued-logic
title: "NULL and three-valued logic"
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

NULL trong SQL là marker cho giá trị thiếu, không phải một giá trị thông thường. Vì T-SQL dùng logic ba giá trị, một predicate có thể cho TRUE, FALSE hoặc UNKNOWN; điều này ảnh hưởng trực tiếp đến WHERE/HAVING, CHECK constraints, UNIQUE constraints, JOIN và subquery.

## Variants

- Predicate filter: WHERE/HAVING chỉ giữ TRUE, loại FALSE và UNKNOWN.
- Constraint: CHECK thường chấp nhận TRUE hoặc UNKNOWN, chỉ loại FALSE.
- Subquery: IN/NOT IN và so sánh với NULL có thể tạo kết quả bất ngờ nếu không xử lý bằng EXISTS/NOT EXISTS hoặc IS NULL/IS NOT NULL đúng cách.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/t-sql-data-modification]]
- [[concepts/relational-query-thinking]]

## Mentioned In

## Notes

