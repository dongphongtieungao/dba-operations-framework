---
id: t-sql-transactions-and-concurrency
title: "T-SQL transactions and concurrency"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/master-dba-runbook-backend-java-azure-sql]]
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/t-sql-data-modification]]
  - [[concepts/sql-server-performance-diagnostics]]
confidence: unverified
---

## Definition

T-SQL transactions and concurrency là nhóm khái niệm về cách SQL Server bảo vệ tính nhất quán khi nhiều session đọc/ghi cùng dữ liệu. Nó bao gồm transaction, ACID, lock, blocking, isolation levels, deadlock và optimistic concurrency; đây là nền tảng để DBA xử lý tranh chấp dữ liệu, blocking chain và lỗi nghiệp vụ do isolation không phù hợp.

## Variants

- Transaction scope: autocommit, explicit transaction và implicit transaction.
- Pessimistic concurrency: dùng lock để ngăn xung đột.
- Optimistic concurrency: dựa trên row versioning hoặc kiểm tra xung đột khi ghi.
- Isolation levels: READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE, SNAPSHOT và các biến thể row-versioning.

## Key Sources

- [[sources/master-dba-runbook-backend-java-azure-sql]]
- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/sql-server-extended-events-monitoring]]

## Mentioned In

## Notes
