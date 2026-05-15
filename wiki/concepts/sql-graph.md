---
id: sql-graph
title: "SQL Graph"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/t-sql]]
  - [[concepts/t-sql-joins]]
confidence: unverified
---

## Definition

SQL Graph là tính năng SQL Server cho phép mô hình hóa dữ liệu bằng node tables và edge tables, rồi truy vấn quan hệ đồ thị bằng các mở rộng T-SQL như MATCH. Tính năng này có thể làm một số truy vấn quan hệ nhiều bước ngắn gọn hơn, nhưng cần cân nhắc vì mô hình graph khác đáng kể so với mô hình quan hệ truyền thống.

## Variants

- Node tables: lưu thực thể trong graph.
- Edge tables: lưu quan hệ giữa các node.
- MATCH queries: mô tả pattern cần tìm trong graph.
- SHORTEST_PATH và graph path aggregates: dùng cho một số bài toán đường đi.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/azure-sql-database]]
- [[concepts/azure-sql-managed-instance]]

## Mentioned In

## Notes

