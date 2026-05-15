---
id: incident-response-and-rca
title: Incident response and RCA
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/master-dba-runbook-backend-java-azure-sql]]
related_concepts:
  - [[concepts/dba-production-mindset]]
  - [[concepts/sql-server-performance-diagnostics]]
  - [[concepts/azure-sql-monitoring-and-observability]]
confidence: unverified
---

## Definition

Incident response and RCA là quy trình xử lý sự cố production theo thứ tự: nhận cảnh báo, xác minh, đánh giá phạm vi, giảm tác động, thu thập bằng chứng, xác định nguyên nhân gốc và ghi báo cáo phòng ngừa tái diễn.

## Variants

- Mitigation-first response: ưu tiên giảm tác động người dùng trước khi phân tích đầy đủ.
- Evidence-based RCA: timeline, impact, root cause, corrective action, preventive action và lessons learned.
- Preventive release review: dùng Query Store, wait stats và metrics ứng dụng để phát hiện regression sau triển khai.

## Key Sources

- [[sources/master-dba-runbook-backend-java-azure-sql]]

## Related Concepts

- [[concepts/dba-production-mindset]]
- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/azure-sql-monitoring-and-observability]]

## Mentioned In

## Notes

