---
id: sql-server-operations-appendix
title: SQL Server Operations Appendix
type: source
created: 2026-05-18
updated: 2026-05-18
authors:
  - DBA Team
year: 2026
importance: 4
urls: []
raw_paths:
  - raw/sources/dba_framework/document_guide/56_sql_server_operations_appendix.md
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: skipped
findings: []
---

## Summary

SQL Server Operations Appendix chuyển các policy và runbook DBA chung thành hướng dẫn vận hành riêng cho SQL Server on-premises, SQL Server on VM và các mô hình dùng Always On, Failover Cluster Instance, SQL Agent, backup native và maintenance job. Tài liệu nguồn đã có khung chính về backup/restore, Always On, CHECKDB, Query Store, SQL Agent, index maintenance và monitoring; bản hoàn thiện trong wiki mở rộng thành một phụ lục vận hành có checklist, evidence, exception và mapping sang runbook cụ thể. Trọng tâm là giúp DBA dùng đúng cơ chế SQL Server nhưng vẫn giữ nguyên lớp kiểm soát chung của DBA Operations Framework.

## Key Claims

- SQL Server appendix không thay thế policy chung; nó dịch policy thành thao tác, kiểm tra và evidence phù hợp với SQL Server. (confidence: high)
- Backup/restore SQL Server phải kiểm soát full, differential, transaction log backup, recovery model, backup chain, restore drill và restore validation. (confidence: high)
- HA/DR SQL Server cần theo dõi Availability Group health, listener, synchronization state, redo/send queue, quorum, replica role và RTO/RPO thực tế. (confidence: high)
- CHECKDB, Query Store, Extended Events, DMVs, SQL Agent và index/statistics maintenance là các cơ chế vận hành lõi cần được đưa vào checklist định kỳ. (confidence: high)
- Mọi ngoại lệ như không bật Query Store, không chạy CHECKDB đầy đủ, forced failover hoặc repair option phải có owner, risk acceptance và approval. (confidence: high)

## Evidence

- Phần mapping vận hành của nguồn nối Backup, Restore, HA, Failover, Integrity, Performance, Job, Index và Monitoring với cơ chế SQL Server tương ứng.
- Phần backup/restore nêu full backup, differential backup, transaction log backup, backup chain và restore validation.
- Phần Always On/failover yêu cầu kiểm tra synchronization health, listener, application connection string, approval và RTO evidence.
- Phần CHECKDB/corruption cảnh báo không dùng repair allow data loss nếu chưa có approval rõ ràng.
- Phần Query Store/performance yêu cầu theo dõi top queries và ghi lý do, thời hạn review, rollback plan khi force plan.

## Related Concepts

- [[concepts/sql-server-operations-appendix]]
- [[concepts/sql-server-administration]]
- [[concepts/sql-server-configuration-baseline]]
- [[concepts/sql-server-backup-restore-operations]]
- [[concepts/sql-server-data-recovery-strategy]]
- [[concepts/high-availability-and-disaster-recovery]]
- [[concepts/sql-server-security-auditing]]
- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/sql-server-query-store-analysis]]
- [[concepts/sql-server-index-and-statistics-maintenance]]
- [[concepts/sql-server-agent-job-operations]]
- [[concepts/sql-server-extended-events-monitoring]]
- [[concepts/sql-server-storage-and-tempdb-operations]]

## Related Sources

- [[sources/sql-server-2022-administration-inside-out]]
- [[sources/sql-server-dba-toolbox]]
- [[sources/dba-operations-framework]]
- [[sources/azure-sql-operations-appendix]]
- [[sources/runbook-backup-verification]]
- [[sources/runbook-restore-validation]]
- [[sources/runbook-failover-execution]]
- [[sources/runbook-add-replica]]
- [[sources/runbook-database-health-check]]
- [[sources/runbook-capacity-report]]

## People

Không có cá nhân cụ thể được tạo hồ sơ trong lần nạp này.

## Open Questions

- Cần quyết định bộ baseline mặc định cho SQL Server theo từng môi trường: production, staging, development và DR.
- Cần bổ sung script chuẩn hoặc liên kết tới toolbox cho từng checklist: backup chain, AG health, CHECKDB, Query Store, job failed, tempdb, log growth và sysadmin audit.
- Cần xác nhận ngưỡng vận hành chính thức cho RPO/RTO, CHECKDB frequency, index maintenance, Query Store retention và capacity forecast.

## Notes

