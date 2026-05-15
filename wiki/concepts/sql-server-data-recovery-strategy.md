---
id: sql-server-data-recovery-strategy
title: SQL Server data recovery strategy
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/sql-server-2022-administration-inside-out]]
related_concepts:
  - [[concepts/sql-server-backup-restore-operations]]
  - [[concepts/business-continuity]]
  - [[concepts/high-availability-and-disaster-recovery]]
confidence: unverified
---

## Definition

SQL Server data recovery strategy là kế hoạch bảo đảm dữ liệu có thể được khôi phục sau lỗi, mất dữ liệu, ransomware hoặc thảm họa. Kế hoạch này cần xác định RPO, RTO, recovery model, loại backup, chuỗi backup, vị trí lưu trữ, quy trình restore và runbook kiểm thử.

## Variants

- Phục hồi theo thời điểm: dùng full, differential và log backups để khôi phục đến thời điểm cụ thể.
- Phục hồi theo file/filegroup: dùng khi database lớn cần khôi phục từng phần.
- Phục hồi hybrid/cloud: kết hợp backup lên URL, storage cloud hoặc chiến lược failover sang Azure.

## Key Sources

- [[sources/sql-server-2022-administration-inside-out]]

## Related Concepts

- [[concepts/sql-server-backup-restore-operations]]
- [[concepts/business-continuity]]
- [[concepts/high-availability-and-disaster-recovery]]

## Mentioned In

## Notes
