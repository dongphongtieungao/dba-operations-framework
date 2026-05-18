---
id: azure-sql-operations-appendix
title: Azure SQL Operations Appendix
type: source
created: 2026-05-18
updated: 2026-05-18
authors:
  - DBA Team
year: 2026
importance: 4
urls: []
raw_paths:
  - raw/sources/dba_framework/document_guide/60_azure_sql_operations_appendix.md
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: skipped
findings: []
---

## Summary

Azure SQL Operations Appendix chuyển các policy DBA chung sang vận hành Azure SQL Database và Azure SQL Managed Instance. Tài liệu nguồn đã có mapping về backup built-in, PITR, Long-Term Retention, HA/DR, failover group, Query Store, Azure Monitor, SQL Agent alternative, firewall, private endpoint, Microsoft Entra ID và TDE; bản hoàn thiện trong wiki bổ sung quyết định vận hành theo service model, evidence, alert, cost/capacity và boundary giữa Azure SQL Database, Managed Instance và SQL Server on Azure VM. Trọng tâm là giúp DBA giữ được kỷ luật backup, DR, bảo mật, monitoring và performance trong môi trường PaaS, nơi Microsoft quản lý nhiều phần hạ tầng nhưng DBA vẫn chịu trách nhiệm dữ liệu, cấu hình và bằng chứng vận hành.

## Key Claims

- Azure SQL có backup tự động, nhưng DBA vẫn phải kiểm tra retention, LTR, restore drill, evidence và khả năng đáp ứng RPO/RTO. (confidence: high)
- Azure SQL Database và Azure SQL Managed Instance cần checklist khác nhau vì Database là mô hình database-level PaaS, còn Managed Instance gần với SQL Server instance hơn. (confidence: high)
- Failover group, geo-replication, geo-restore, zone redundancy, private endpoint và application connection strategy là phần vận hành DR quan trọng. (confidence: high)
- Azure Monitor, diagnostic settings, Query Store, automatic tuning setting, wait statistics, connection failure và storage/cost metric phải được đưa vào health check. (confidence: high)
- Khi thiếu SQL Agent trên Azure SQL Database, DBA phải dùng Elastic Jobs, Azure Automation, Functions, Logic Apps hoặc Data Factory theo chuẩn evidence và change control. (confidence: medium)

## Evidence

- Mapping dịch vụ của nguồn phân biệt Azure SQL Database và Managed Instance theo backup, HA, DR, job, monitoring, performance và security.
- Phần backup/PITR yêu cầu kiểm tra retention, Long-Term Retention, restore drill, geo restore hoặc failover group.
- Phần failover/DR nhấn mạnh failover group endpoint, connection strategy, RTO/RPO thực tế và post-check connectivity.
- Phần monitoring liệt kê diagnostic settings, Azure Monitor, DTU/vCore utilization, CPU, data IO, log IO, storage, deadlock và connection failure.
- Phần SQL Agent alternative nêu Elastic Jobs, Azure Automation, Logic Apps, Functions, Data Factory và SQL Agent trên Managed Instance.

## Related Concepts

- [[concepts/azure-sql-operations-appendix]]
- [[concepts/azure-sql]]
- [[concepts/azure-sql-database]]
- [[concepts/azure-sql-managed-instance]]
- [[concepts/sql-server-on-azure-vms]]
- [[concepts/azure-sql-monitoring-and-observability]]
- [[concepts/high-availability-and-disaster-recovery]]
- [[concepts/business-continuity]]
- [[concepts/private-endpoints]]
- [[concepts/hyperscale]]
- [[concepts/database-capacity-reporting]]

## Related Sources

- [[sources/azure-sql-documentation]]
- [[sources/sql-server-2022-administration-inside-out]]
- [[sources/dba-operations-framework]]
- [[sources/sql-server-operations-appendix]]
- [[sources/runbook-backup-verification]]
- [[sources/runbook-restore-validation]]
- [[sources/runbook-failover-execution]]
- [[sources/runbook-database-health-check]]
- [[sources/runbook-capacity-report]]

## People

Không có cá nhân cụ thể được tạo hồ sơ trong lần nạp này.

## Open Questions

- Cần đối chiếu với Microsoft Learn mới nhất trước khi chốt các giới hạn/tính năng thay đổi nhanh như automatic tuning, failover group, serverless, Hyperscale, private endpoint và telemetry.
- Cần xác định chuẩn alert riêng cho DTU/vCore, storage, deadlock, blocked sessions, connection failure, failover event và cost anomaly.
- Cần bổ sung runbook riêng cho Elastic Jobs/Azure Automation nếu Azure SQL Database là nền tảng chính.

## Notes

