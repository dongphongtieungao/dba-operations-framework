---
id: database-replica-provisioning
title: Database Replica Provisioning
type: concept
created: 2026-05-18
updated: 2026-05-18
key_sources:
  - [[sources/runbook-add-replica]]
related_concepts:
  - [[concepts/high-availability-and-disaster-recovery]]
  - [[concepts/business-continuity]]
  - [[concepts/dba-failover-execution-runbook]]
confidence: unverified
---

## Definition

Database Replica Provisioning là quy trình thêm replica mới vào topology database để phục vụ HA/DR, đọc phụ trợ hoặc mục tiêu vận hành khác. Quy trình cần kiểm tra source, target, seed data, network, replication credential, lag, monitoring, alert, inventory và rollback.

## Variants

- Synchronous replica: ưu tiên giảm mất dữ liệu nhưng nhạy với latency.
- Asynchronous replica: phù hợp DR hoặc read replica nhưng cần theo dõi lag.
- Log shipping hoặc streaming replication: dùng backup/log/WAL/binlog để đồng bộ.
- Platform-managed replica: dùng cơ chế nền tảng như Azure SQL geo-replication hoặc failover group.

## Key Sources

- [[sources/runbook-add-replica]]

## Related Concepts

- [[concepts/high-availability-and-disaster-recovery]]
- [[concepts/business-continuity]]
- [[concepts/dba-failover-execution-runbook]]
- [[concepts/azure-sql-monitoring-and-observability]]
- [[concepts/dba-evidence-driven-operations]]

## Mentioned In

## Notes

