---
id: azure-sql-monitoring-and-observability
title: Azure SQL monitoring and observability
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/master-dba-runbook-backend-java-azure-sql]]
  - [[sources/runbook-database-health-check]]
  - [[sources/runbook-capacity-report]]
  - [[sources/azure-sql-operations-appendix]]
related_concepts:
  - [[concepts/azure-sql-database]]
  - [[concepts/sql-server-performance-diagnostics]]
  - [[concepts/sql-server-query-store-analysis]]
  - [[concepts/database-health-check-runbook]]
  - [[concepts/database-capacity-reporting]]
  - [[concepts/azure-sql-operations-appendix]]
confidence: unverified
---

## Definition

Azure SQL monitoring and observability là thực hành theo dõi Azure SQL Database bằng metric, Query Store, wait statistics, session/request state, connection error, timeout rate và tín hiệu từ ứng dụng. Mục tiêu là phân biệt database slow, app slow, network slow hay connection pool cạn.

## Variants

- Database-side monitoring: CPU, IO, waits, Query Store, blocking và TempDB usage.
- Application-side monitoring: HikariCP connection pool, API timeout, error rate và latency.
- Release monitoring: so sánh top query, plan regression và wait trend sau khi deploy.

## Key Sources

- [[sources/master-dba-runbook-backend-java-azure-sql]]
- [[sources/runbook-database-health-check]]
- [[sources/runbook-capacity-report]]
- [[sources/azure-sql-operations-appendix]]

## Related Concepts

- [[concepts/azure-sql-database]]
- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/sql-server-query-store-analysis]]
- [[concepts/database-health-check-runbook]]
- [[concepts/database-capacity-reporting]]
- [[concepts/azure-sql-operations-appendix]]

## Mentioned In

## Notes
