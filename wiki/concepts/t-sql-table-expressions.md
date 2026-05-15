---
id: t-sql-table-expressions
title: "T-SQL table expressions"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/t-sql-subqueries]]
  - [[concepts/t-sql-window-functions]]
confidence: unverified
---

## Definition

T-SQL table expressions là các biểu thức trả về kết quả dạng bảng để query khác có thể tham chiếu như một nguồn dữ liệu. Chúng gồm derived tables, common table expressions, views và inline table-valued functions, giúp chia nhỏ logic truy vấn, tái sử dụng và giải quyết các pha logic không thể viết trực tiếp trong cùng một SELECT.

## Variants

- Derived table: biểu thức bảng ẩn danh trong FROM.
- Common table expression: biểu thức bảng đặt tên bằng WITH.
- View: biểu thức bảng được lưu dưới dạng object.
- Inline table-valued function: biểu thức bảng có tham số.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/logical-query-processing]]
- [[concepts/t-sql-data-modification]]

## Mentioned In

## Notes

