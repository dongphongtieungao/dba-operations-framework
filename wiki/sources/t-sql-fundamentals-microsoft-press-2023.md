---
id: t-sql-fundamentals-microsoft-press-2023
title: T-SQL Fundamentals
type: source
created: 2026-05-15
updated: 2026-05-15
authors:
  - Itzik Ben-Gan
year: 2023
importance: 4
raw_paths:
  - raw/sources/T-SQL Fundamentals-Microsoft Press (2023).txt
provenance: replayable
confidence: medium
ingest_status: finalized
verify_status: passed
findings: []
---

## Summary

T-SQL Fundamentals là sách nền tảng của Itzik Ben-Gan về cách truy vấn, sửa dữ liệu và lập trình bằng Transact-SQL trên SQL Server/Azure SQL. Sách không chỉ liệt kê cú pháp, mà nhấn mạnh tư duy quan hệ, xử lý truy vấn theo logic, NULL/logic ba giá trị, table expressions, window functions, transaction/concurrency và các chủ đề hiện đại như temporal tables, SQL Graph. Với wiki DBA này, nguồn này là nền móng để hiểu đúng hành vi T-SQL trước khi tối ưu, migration hoặc vận hành workload SQL Server/Azure SQL.

## Key Claims

- T-SQL là dialect của SQL chuẩn cho hệ sinh thái Microsoft, có cả phần theo chuẩn và phần mở rộng riêng; cần hiểu cả logic SQL khai báo lẫn khác biệt T-SQL cụ thể. (confidence: high)
- Nền tảng truy vấn T-SQL dựa trên mô hình quan hệ, set theory và predicate logic; vì vậy người viết query nên nghĩ theo tập hợp thay vì từng dòng tuần tự. (confidence: high)
- [[concepts/logical-query-processing]] là khung cần thiết để hiểu vì sao các mệnh đề SELECT, FROM, WHERE, GROUP BY, HAVING, ORDER BY, TOP/OFFSET và window functions hành xử như vậy. (confidence: high)
- [[concepts/null-and-three-valued-logic]] là nguồn gây nhiều lỗi thực tế, đặc biệt trong WHERE/HAVING, CHECK constraints, UNIQUE, subqueries và so sánh giá trị thiếu. (confidence: high)
- Các chủ đề như [[concepts/t-sql-joins]], [[concepts/t-sql-subqueries]], [[concepts/t-sql-table-expressions]], [[concepts/t-sql-set-operators]] và [[concepts/t-sql-window-functions]] tạo nền cho cả truy vấn phân tích lẫn tối ưu workload. (confidence: high)
- Phần [[concepts/t-sql-transactions-and-concurrency]] giúp DBA hiểu transaction, lock, blocking, isolation level và row-versioning trước khi xử lý tranh chấp dữ liệu trong hệ thống thật. (confidence: high)

## Evidence

- Mục lục cho thấy sách gồm 12 chương: nền tảng T-SQL, single-table queries, joins, subqueries, table expressions, set operators, phân tích dữ liệu, data modification, temporal tables, transactions/concurrency, SQL Graph và programmable objects.
- Phần giới thiệu nói sách hướng tới T-SQL developers, DBAs, BI practitioners, data scientists, report writers, analysts, architects và SQL Server power users cần viết query và code T-SQL.
- Chương 1 giải thích SQL/T-SQL là ngôn ngữ khai báo dựa trên relational model; sách tập trung vào DML gồm SELECT, INSERT, UPDATE, DELETE, TRUNCATE và MERGE.
- Chương 2 nhấn mạnh xử lý truy vấn theo logic, predicate trong WHERE/HAVING và hệ quả của NULL/UNKNOWN.
- Chương 7 trình bày window functions, pivot/unpivot, grouping sets và time series; Chương 10 trình bày transaction, lock, blocking, isolation level, deadlock và optimistic concurrency; Chương 11 trình bày SQL Graph với node/edge và MATCH.

## Related Concepts

- [[concepts/t-sql]]
- [[concepts/relational-query-thinking]]
- [[concepts/logical-query-processing]]
- [[concepts/null-and-three-valued-logic]]
- [[concepts/t-sql-joins]]
- [[concepts/t-sql-subqueries]]
- [[concepts/t-sql-table-expressions]]
- [[concepts/t-sql-set-operators]]
- [[concepts/t-sql-window-functions]]
- [[concepts/t-sql-data-modification]]
- [[concepts/system-versioned-temporal-tables]]
- [[concepts/t-sql-transactions-and-concurrency]]
- [[concepts/sql-graph]]
- [[concepts/t-sql-programmable-objects]]

## Related Sources

- [[sources/azure-sql-documentation]]
- [[sources/sql-server-dba-toolbox]]

## People

- [[people/itzik-ben-gan]]

## Open Questions

- Trang này đang nạp ở mức toàn sách. Nếu cần học theo chương, nên tách thêm các trang chuyên sâu cho Chapter 2, Chapter 7 và Chapter 10 vì đây là các phần liên quan nhiều nhất đến truy vấn, phân tích và vận hành.
- Tệp nguồn là bản text trích xuất từ sách nên có một số ký tự OCR lỗi; các luận điểm trong wiki chỉ dựa trên phần cấu trúc, mục lục và các đoạn văn đủ rõ để đối chiếu.

## Notes

