---
id: database-capacity-reporting
title: Database Capacity Reporting
type: concept
created: 2026-05-18
updated: 2026-05-18
key_sources:
  - [[sources/runbook-capacity-report]]
related_concepts:
  - [[concepts/dba-operations-kpi-and-risk]]
  - [[concepts/azure-sql-monitoring-and-observability]]
  - [[concepts/sql-server-storage-and-tempdb-operations]]
  - [[concepts/dba-evidence-driven-operations]]
confidence: unverified
---

## Definition

Database Capacity Reporting là thực hành tổng hợp dung lượng hiện tại, lịch sử tăng trưởng và forecast để dự báo rủi ro hết dung lượng trước khi ảnh hưởng dịch vụ. Báo cáo tốt không chỉ nêu số liệu mà còn chỉ rõ top risk, ngày dự kiến chạm ngưỡng, hành động khuyến nghị và owner xử lý.

## Variants

- Daily capacity watch: cảnh báo ngắn cho các database hoặc disk sắp chạm ngưỡng.
- Monthly capacity report: báo cáo định kỳ cho service owner và DBA lead.
- Forecast-driven action list: tạo ticket từ dự báo capacity thay vì chờ alert critical.
- Backup capacity view: theo dõi backup size và retention để tránh thiếu storage lưu trữ.

## Key Sources

- [[sources/runbook-capacity-report]]

## Related Concepts

- [[concepts/dba-operations-kpi-and-risk]]
- [[concepts/azure-sql-monitoring-and-observability]]
- [[concepts/sql-server-storage-and-tempdb-operations]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-policy-sop-runbook-layering]]

## Mentioned In

## Notes

