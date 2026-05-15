---
id: relational-query-thinking
title: "Relational query thinking"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/t-sql]]
  - [[concepts/logical-query-processing]]
confidence: unverified
---

## Definition

Relational query thinking là cách viết SQL theo tư duy tập hợp, predicate và quan hệ, thay vì mô phỏng vòng lặp từng dòng như trong ngôn ngữ thủ tục. Với T-SQL, tư duy này giúp viết truy vấn rõ hơn, tránh lỗi logic và tạo điều kiện để optimizer chọn kế hoạch thực thi tốt.

## Variants

- Tư duy theo tập hợp: xem bảng/kết quả là một tập hoặc multiset.
- Tư duy predicate: mô tả điều kiện để dữ liệu thuộc tập kết quả.
- Tư duy khai báo: nói kết quả cần gì, không ép cách xử lý vật lý.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/t-sql-set-operators]]
- [[concepts/null-and-three-valued-logic]]

## Mentioned In

## Notes

