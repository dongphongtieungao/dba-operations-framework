---
id: dba-production-mindset
title: DBA production mindset
type: concept
created: 2026-05-15
updated: 2026-05-15
key_sources:
  - [[sources/master-dba-runbook-backend-java-azure-sql]]
related_concepts:
  - [[concepts/sql-server-administration]]
  - [[concepts/sql-server-performance-diagnostics]]
  - [[concepts/incident-response-and-rca]]
confidence: unverified
---

## Definition

DBA production mindset là cách tiếp cận vận hành database dựa trên bằng chứng, giảm rủi ro và ưu tiên ổn định dịch vụ. Thay vì đoán nguyên nhân hoặc thay đổi ngay, DBA thu thập metric, log, wait stats, execution plan, blocking và thông tin ứng dụng để xác định đúng bottleneck.

## Variants

- Mindset điều tra: đọc tín hiệu trước khi kết luận.
- Mindset giảm thiểu tác động: xử lý ảnh hưởng người dùng trước, phân tích sâu sau.
- Mindset phòng ngừa: đưa review plan, monitoring và checklist sau release vào quy trình thường kỳ.

## Key Sources

- [[sources/master-dba-runbook-backend-java-azure-sql]]

## Related Concepts

- [[concepts/sql-server-administration]]
- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/incident-response-and-rca]]

## Mentioned In

## Notes

