---
id: system-versioned-temporal-tables
title: "System-versioned temporal tables"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/t-sql-fundamentals-microsoft-press-2023]]
related_concepts:
  - [[concepts/t-sql-data-modification]]
  - [[concepts/business-continuity]]
confidence: unverified
---

## Definition

System-versioned temporal tables là tính năng SQL Server/Azure SQL lưu lịch sử thay đổi của hàng theo thời gian hệ thống, cho phép truy vấn dữ liệu ở thời điểm hoặc khoảng thời gian trước đó. Chúng hữu ích cho audit, điều tra thay đổi và một số nhu cầu phục hồi logic, nhưng không thay thế backup/restore hoặc thiết kế audit đầy đủ.

## Variants

- Tạo temporal table: bảng hiện tại, bảng history và period columns.
- Sửa dữ liệu: hệ thống tự ghi phiên bản cũ vào history table.
- Truy vấn temporal: dùng FOR SYSTEM_TIME để xem dữ liệu theo thời gian.

## Key Sources

- [[sources/t-sql-fundamentals-microsoft-press-2023]]

## Related Concepts

- [[concepts/azure-sql-database]]
- [[concepts/azure-sql-managed-instance]]

## Mentioned In

## Notes

