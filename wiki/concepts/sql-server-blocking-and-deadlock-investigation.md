---
id: sql-server-blocking-and-deadlock-investigation
title: SQL Server blocking and deadlock investigation
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/master-dba-runbook-backend-java-azure-sql]]
related_concepts:
  - [[concepts/t-sql-transactions-and-concurrency]]
  - [[concepts/sql-server-extended-events-monitoring]]
  - [[concepts/sql-server-performance-diagnostics]]
confidence: unverified
---

## Definition

SQL Server blocking and deadlock investigation là quy trình tìm session, transaction, object và câu lệnh gây chờ lock hoặc deadlock. DBA cần xác định blocking chain, deadlock graph, thứ tự truy cập object, phạm vi transaction và các scan rộng do thiếu index để sửa đúng nguyên nhân.

## Variants

- Blocking investigation: tìm session đang giữ lock và session đang chờ.
- Deadlock investigation: đọc deadlock graph để hiểu vòng chờ và victim.
- Transaction-scope review: thu hẹp transaction, tránh giữ lock trong khi gọi API hoặc xử lý logic ngoài database.

## Key Sources

- [[sources/master-dba-runbook-backend-java-azure-sql]]

## Related Concepts

- [[concepts/t-sql-transactions-and-concurrency]]
- [[concepts/sql-server-extended-events-monitoring]]
- [[concepts/sql-server-performance-diagnostics]]

## Mentioned In

## Notes

