---
id: logical-query-processing
title: "Logical query processing"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/t-sql]]
  - [[concepts/null-and-three-valued-logic]]
confidence: unverified
---

## Definition

Logical query processing là mô hình khái niệm mô tả thứ tự logic mà một truy vấn SELECT được hiểu: từ nguồn dữ liệu, lọc, nhóm, lọc nhóm, tính biểu thức, sắp xếp và giới hạn kết quả. Đây không phải thứ tự thực thi vật lý bắt buộc, nhưng là cách đúng để giải thích alias, aggregate, window function, ORDER BY và các lỗi thường gặp.

## Variants

- Single-table SELECT: FROM, WHERE, GROUP BY, HAVING, SELECT, ORDER BY, TOP/OFFSET.
- Query có JOIN: FROM bao gồm các table operator trước khi lọc WHERE.
- Query phân tích: window functions xuất hiện ở SELECT/ORDER BY và có khung xử lý riêng qua OVER.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/t-sql-joins]]
- [[concepts/t-sql-window-functions]]
- [[concepts/t-sql-table-expressions]]

## Mentioned In

## Notes

