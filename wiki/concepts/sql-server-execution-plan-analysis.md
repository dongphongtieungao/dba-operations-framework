---
id: sql-server-execution-plan-analysis
title: SQL Server execution plan analysis
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/master-dba-runbook-backend-java-azure-sql]]
related_concepts:
  - [[concepts/sql-server-performance-diagnostics]]
  - [[concepts/sql-server-index-and-statistics-maintenance]]
  - [[concepts/sql-server-query-store-analysis]]
confidence: unverified
---

## Definition

SQL Server execution plan analysis là kỹ năng đọc kế hoạch thực thi để hiểu SQL Server thực sự đang xử lý query như thế nào. DBA dùng plan để tìm scan lớn, seek, lookup, sort/hash tốn tài nguyên, spill, implicit conversion, sai lệch estimated/actual rows và dấu hiệu thiếu index hoặc statistics lỗi thời.

## Variants

- Estimated plan: plan dự kiến trước khi query chạy.
- Actual plan: plan sau khi chạy, có thông tin row count thực tế.
- Plan regression analysis: so sánh plan cũ và mới, thường kết hợp với Query Store.

## Key Sources

- [[sources/master-dba-runbook-backend-java-azure-sql]]

## Related Concepts

- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/sql-server-index-and-statistics-maintenance]]
- [[concepts/sql-server-query-store-analysis]]

## Mentioned In

## Notes

