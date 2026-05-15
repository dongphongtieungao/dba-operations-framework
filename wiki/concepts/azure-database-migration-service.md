---
id: azure-database-migration-service
title: "Azure Database Migration Service"
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/azure-database-migration-service-documentation]]
  - [[sources/azure-database-migration-guides]]
related_concepts:
  - [[concepts/database-migration]]
  - [[concepts/online-and-offline-migration]]
  - [[concepts/migration-assessment]]
confidence: unverified
---

## Definition

Azure Database Migration Service là dịch vụ được quản lý để thực thi migration cơ sở dữ liệu lên Azure, đặc biệt hữu ích khi cần điều phối migration SQL Server tới Azure SQL Database, Azure SQL Managed Instance hoặc SQL Server on Azure VMs với lựa chọn online/offline tùy kịch bản.

## Variants

- Migration offline: chấp nhận downtime dài hơn, quy trình thường đơn giản hơn.
- Migration online: giảm downtime bằng cách đồng bộ trong khi nguồn vẫn hoạt động, rồi cutover.

## Key Sources

- [[sources/azure-database-migration-service-documentation]]
- [[sources/azure-database-migration-guides]]

## Related Concepts

- [[concepts/azure-migrate]]
- [[concepts/sql-server-migration-assistant]]
- [[concepts/private-endpoints]]
- [[concepts/transparent-data-encryption]]

## Mentioned In

## Notes

