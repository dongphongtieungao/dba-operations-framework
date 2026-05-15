---
id: t-sql-data-modification
title: "T-SQL data modification"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/t-sql-transactions-and-concurrency]]
  - [[concepts/null-and-three-valued-logic]]
confidence: unverified
---

## Definition

T-SQL data modification là nhóm câu lệnh thay đổi dữ liệu gồm INSERT, SELECT INTO, BULK INSERT, DELETE, TRUNCATE, UPDATE, MERGE và OUTPUT. Với DBA, phần này quan trọng vì data modification liên quan trực tiếp đến transaction log, lock, trigger, constraint, audit và khả năng rollback khi thao tác sai.

## Variants

- Insert: VALUES, INSERT SELECT, INSERT EXEC, SELECT INTO, BULK INSERT.
- Delete/truncate: xóa theo predicate hoặc xóa nhanh toàn bảng theo điều kiện phù hợp.
- Update/merge: cập nhật theo predicate, join hoặc đồng bộ nguồn-đích.
- OUTPUT: ghi lại dữ liệu inserted/deleted cho audit hoặc xử lý tiếp.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/t-sql-table-expressions]]
- [[concepts/sql-server-security-auditing]]

## Mentioned In

## Notes

