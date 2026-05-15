---
id: database-migration
title: "Database migration"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/sql-server-2022-administration-inside-out]]
  - [[sources/azure-sql-documentation]]
  - [[sources/azure-database-migration-service-documentation]]
  - [[sources/azure-database-migration-guides]]
related_concepts:
  - [[concepts/cloud-migration-phases]]
  - [[concepts/migration-assessment]]
  - [[concepts/online-and-offline-migration]]
confidence: unverified
---

## Definition

Database migration là quá trình chuyển cơ sở dữ liệu, schema, dữ liệu, cấu hình bảo mật và workload liên quan từ nguồn hiện tại sang nền tảng đích, trong wiki này chủ yếu là từ SQL Server hoặc hệ quản trị khác sang Azure SQL và các dịch vụ dữ liệu Azure.

## Variants

- Lift-and-shift: chuyển gần nguyên trạng, thường phù hợp với SQL Server on Azure VMs.
- Modernization: chuyển sang dịch vụ được quản lý hơn như Azure SQL Database hoặc Azure SQL Managed Instance.
- Heterogeneous migration: chuyển đổi giữa các engine khác nhau, thường cần schema conversion.

## Key Sources

- [[sources/sql-server-2022-administration-inside-out]]
- [[sources/azure-sql-documentation]]
- [[sources/azure-database-migration-service-documentation]]
- [[sources/azure-database-migration-guides]]

## Related Concepts

- [[concepts/azure-database-migration-service]]
- [[concepts/azure-migrate]]
- [[concepts/sql-server-migration-assistant]]

## Mentioned In

## Notes
