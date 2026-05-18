---
id: runbook-failover-execution
title: Runbook Failover Execution
type: source
created: 2026-05-18
updated: 2026-05-18
authors:
  - DBA Team
year: 2026
importance: 4
urls: []
raw_paths:
  - raw/sources/dba_framework/document_guide/41_runbook_failover_execution.md
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: skipped
findings: []
---

## Summary

Runbook Failover Execution chuẩn hóa thao tác chuyển vai trò primary trong các kịch bản planned switchover, unplanned failover, DR failover và drill. Tài liệu nhấn mạnh phê duyệt, kiểm tra trạng thái dữ liệu, freeze thay đổi, thực thi failover, kiểm tra database/application sau failover, lưu evidence và đo RTO/RPO thực tế. Điểm tối ưu quan trọng của runbook là biến failover thành chuỗi bước có điều kiện dừng, tiêu chí rollback/fallback và kiểm chứng endpoint rõ ràng thay vì thao tác thủ công rời rạc.

## Key Claims

- Failover production là thao tác rủi ro cao, cần approval hoặc emergency approval trước khi thực hiện. (confidence: high)
- Không nên failover khi chưa xác định replication state, target primary, nguy cơ mất dữ liệu và rủi ro split brain, trừ khi emergency approver chấp nhận tiếp tục. (confidence: high)
- Sau failover phải kiểm tra primary mới, replica, endpoint, backup policy, monitoring, alert và smoke test ứng dụng. (confidence: high)
- RTO/RPO thực tế, timeline và evidence là phần bắt buộc để phục vụ báo cáo drill, incident review hoặc RCA. (confidence: high)
- Runbook cần tách phần kiểm soát chung khỏi thao tác riêng theo DBMS như SQL Server Always On, PostgreSQL streaming replication, MySQL/MariaDB GTID, Oracle Data Guard và Azure SQL failover group. (confidence: medium)

## Evidence

- Phần pre-check yêu cầu xác nhận ticket, approval, phạm vi failover, trạng thái current/target primary, replication lag, backup gần nhất, monitoring và rollback/fallback plan.
- Phần thực thi mô tả chuỗi promote, xác nhận primary role, cập nhật listener/DNS/VIP/connection string/failover group hoặc endpoint, refresh connection pool nếu cần và rejoin replica.
- Phần post-check database bao phủ writable primary, fencing old primary, replica connected, replication lag, consistency, backup status, monitoring và alert.
- Phần evidence yêu cầu approval, pre-check report, execution log, timeline, verification primary mới, endpoint verification, application smoke test, dashboard export, RTO/RPO calculation và stakeholder confirmation.

## Related Concepts

- [[concepts/dba-failover-execution-runbook]]
- [[concepts/high-availability-and-disaster-recovery]]
- [[concepts/business-continuity]]
- [[concepts/incident-response-and-rca]]
- [[concepts/dba-policy-sop-runbook-layering]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-operations-kpi-and-risk]]
- [[concepts/azure-sql-monitoring-and-observability]]

## Related Sources

- [[sources/dba-operations-framework]]
- [[sources/runbook-database-health-check]]
- [[sources/runbook-capacity-report]]

## People

Không có cá nhân cụ thể được tạo hồ sơ trong lần nạp này.

## Open Questions

- Cần bổ sung script hoặc playbook chuẩn cho từng nền tảng cụ thể nếu muốn biến runbook này thành tự động hóa thực thi thật.
- Cần xác định ngưỡng RPO/RTO theo từng service tier để điều kiện dừng và tiêu chí rollback không chỉ là mô tả chung.
- Cần chuẩn hóa mẫu evidence path và nơi lưu artifact để mọi lần failover có thể audit lại.

## Notes
