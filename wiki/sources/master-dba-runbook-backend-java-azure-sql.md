---
id: master-dba-runbook-backend-java-azure-sql
title: Master DBA Runbook - Backend Java to Azure SQL
type: source
created: 2026-05-15
updated: 2026-05-15
authors:
  - DBA Training Series
year: 2025
importance: 3
urls: []
raw_paths:
  - raw/sources/7days_for_dev2dba.md
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: passed
findings: []
---

## Summary

Master DBA Runbook - Backend Java to Azure SQL là tài liệu đào tạo 7 ngày giúp backend Java developer, junior DBA hoặc DevOps/SRE chuyển sang tư duy vận hành Azure SQL Database ở mức production. Runbook nhấn mạnh cách điều tra bằng bằng chứng: đọc metric, wait stats, execution plan, Query Store, blocking/deadlock, connection pool và timeline incident trước khi kết luận hoặc thay đổi hệ thống. Với wiki này, nguồn này bổ sung lớp thực hành vận hành hằng ngày: checklist, quy trình phản ứng sự cố, RCA và các nguyên tắc tránh thao tác nguy hiểm trong production.

## Key Claims

- DBA production không nên đoán nguyên nhân query chậm; cần thu thập evidence từ metric, log, wait stats, execution plan, blocking và connection pool trước khi hành động. (confidence: high)
- Logical reads, physical reads, CPU time và elapsed time là các tín hiệu cơ bản để phân biệt query đọc quá nhiều dữ liệu, thiếu cache, nghẽn CPU hoặc chờ tài nguyên. (confidence: high)
- Execution plan và index phải được đọc cùng nhau; không nên thêm index chỉ vì query chậm nếu chưa thấy bằng chứng từ plan, IO và workload. (confidence: high)
- Blocking, deadlock và transaction scope là rủi ro lớn trong ứng dụng backend; transaction dài hoặc gọi API trong transaction có thể làm lock bị giữ quá lâu. (confidence: high)
- Query Store là công cụ trọng tâm để tìm query regression, so sánh plan, phân tích duration/CPU và mitigation bằng force plan khi phù hợp. (confidence: high)
- Với Azure SQL Database, DBA cần hiểu tier, DTU/vCore, monitoring, connection issue và ranh giới giữa vấn đề database với vấn đề ứng dụng hoặc network. (confidence: medium)
- Incident response nên ưu tiên mitigate trước, RCA sau; báo cáo RCA cần timeline, impact, root cause, corrective action, preventive action và lessons learned. (confidence: high)

## Evidence

- Tài liệu chia thành 7 ngày học: SQL fundamentals/IO, execution plan/index, blocking/deadlock, Query Store/parameter sniffing, Azure SQL Database, monitoring/observability và incident response/RCA.
- Phần DBA Mindset nêu rõ quy trình: thu thập evidence, đọc metric, đọc execution plan, xác định root cause, mitigate trước và RCA sau.
- Các checklist cuối tài liệu bao phủ query chậm, CPU cao, API timeout, deadlock và kiểm tra sau release.
- Runbook có ví dụ cụ thể về `SET STATISTICS IO`, `SET STATISTICS TIME`, logical reads, physical reads, Query Store, wait statistics, HikariCP metrics và deadlock graph.
- Production golden rules cảnh báo không restart SQL Server, không kill session bừa, không thêm index vô tội vạ, không đoán root cause và không giữ transaction dài.

## Related Concepts

- [[concepts/dba-production-mindset]]
- [[concepts/sql-server-execution-plan-analysis]]
- [[concepts/sql-server-blocking-and-deadlock-investigation]]
- [[concepts/azure-sql-monitoring-and-observability]]
- [[concepts/incident-response-and-rca]]
- [[concepts/sql-server-administration]]
- [[concepts/sql-server-performance-diagnostics]]
- [[concepts/sql-server-query-store-analysis]]
- [[concepts/sql-server-index-and-statistics-maintenance]]
- [[concepts/sql-server-extended-events-monitoring]]
- [[concepts/t-sql-transactions-and-concurrency]]
- [[concepts/azure-sql-database]]

## Related Sources

- [[sources/sql-server-2022-administration-inside-out]]
- [[sources/sql-server-dba-toolbox]]
- [[sources/azure-sql-documentation]]

## People

Không có cá nhân cụ thể được tạo hồ sơ; nguồn ghi tác giả là DBA Training Series.

## Open Questions

- Cần đối chiếu thêm các phần Azure SQL tier, DTU/vCore và best practice monitoring với tài liệu Microsoft Learn hiện hành trước khi dùng làm chuẩn chính thức.
- Có nên tách runbook này thành các trang output/checklist riêng cho "query chậm", "API timeout", "deadlock" và "sau release" để dùng trực tiếp trong vận hành?

## Notes

