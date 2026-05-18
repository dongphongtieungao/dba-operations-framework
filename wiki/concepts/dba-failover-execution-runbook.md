---
id: dba-failover-execution-runbook
title: DBA Failover Execution Runbook
type: concept
created: 2026-05-18
updated: 2026-05-18
key_sources:
  - [[sources/runbook-failover-execution]]
related_concepts:
  - [[concepts/high-availability-and-disaster-recovery]]
  - [[concepts/business-continuity]]
  - [[concepts/incident-response-and-rca]]
  - [[concepts/dba-evidence-driven-operations]]
confidence: unverified
---

## Definition

DBA Failover Execution Runbook là quy trình thực thi failover có kiểm soát, dùng để chuyển primary sang node hoặc site khác trong planned switchover, unplanned failover, DR failover hoặc drill. Nó kết hợp approval, pre-check, freeze thay đổi, promote/failover, kiểm tra endpoint, kiểm tra ứng dụng, đo RTO/RPO và lưu evidence.

## Variants

- Planned switchover: chuyển primary có kế hoạch, thường có mục tiêu không mất dữ liệu.
- Unplanned failover: chuyển sang standby khi primary lỗi hoặc không còn đáp ứng.
- DR failover: chuyển sang site DR theo crisis process hoặc DR approval.
- Failover drill: diễn tập định kỳ để kiểm chứng quy trình, người tham gia và bằng chứng.

## Key Sources

- [[sources/runbook-failover-execution]]

## Related Concepts

- [[concepts/high-availability-and-disaster-recovery]]
- [[concepts/business-continuity]]
- [[concepts/incident-response-and-rca]]
- [[concepts/dba-policy-sop-runbook-layering]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-operations-kpi-and-risk]]
- [[concepts/azure-sql-monitoring-and-observability]]

## Mentioned In

## Notes

