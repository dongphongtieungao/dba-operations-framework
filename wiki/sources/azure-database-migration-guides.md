---
id: azure-database-migration-guides
title: Azure Database Migration Guides
type: source
created: 2026-05-15
updated: 2026-05-15
authors:
  - Microsoft Learn
year: 2026
importance: 4
urls:
  - "https://learn.microsoft.com/data-migration/"
raw_paths:
  - raw/sources/data-migration.pdf
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: passed
findings: []
---

## Summary

Tài liệu này là bộ hướng dẫn migration theo nguồn và đích, bao phủ SQL Server, Oracle, Db2, Access, SAP ASE, MySQL, PostgreSQL, Azure Synapse Analytics, NoSQL và các đích Azure/Fabric. Với SQL Server, tài liệu trình bày hành trình năm pha gồm discovery, business case, assessment, conversion và migration, rồi dẫn đến các lựa chọn đích như [[concepts/azure-sql-database]], [[concepts/azure-sql-managed-instance]] và [[concepts/sql-server-on-azure-vms]]. Đây là nguồn định hướng tốt cho DBA khi cần lập kế hoạch migration có kiểm chứng, chọn công cụ, đánh giá readiness và tổ chức cutover.

## Key Claims

- Migration SQL Server lên Azure SQL nên đi qua năm pha: discovery, business case, assessment, conversion và migration. (confidence: medium)
- [[concepts/azure-migrate]] hỗ trợ discovery, assessment ở quy mô data estate và xây dựng business case dựa trên TCO, cash flow, utilization và quick wins. (confidence: medium)
- [[concepts/migration-assessment]] dùng dữ liệu cấu hình, sử dụng và hiệu năng để đo readiness và ảnh hưởng khi chuyển SQL Server sang các đích Azure SQL. (confidence: medium)
- [[concepts/sql-server-migration-assistant]] phù hợp khi cần chuyển đổi schema hoặc đối tượng database giữa các engine khác nhau. (confidence: medium)
- Lựa chọn công cụ migration cần dựa trên nguồn, đích, chế độ online/offline, mức độ downtime chấp nhận được, quyền truy cập và yêu cầu chuyển đổi. (confidence: medium)

## Evidence

- Phần "Migration overview: From SQL Server" mô tả rõ năm pha migration.
- Tài liệu nêu vai trò của Azure Migrate appliance trong discovery agentless cho SQL Server instances và databases.
- Phần business case liệt kê TCO, cashflow, utilization insights, quick wins và chuyển từ CapEx sang OpEx.
- Các hướng dẫn riêng cho SQL Server to Azure SQL Managed Instance, Azure SQL Database và SQL Server on Azure VMs cho thấy quyết định target là một bước thiết kế quan trọng, không chỉ là thao tác công cụ.

## Related Concepts

- [[concepts/database-migration]]
- [[concepts/cloud-migration-phases]]
- [[concepts/discovery]]
- [[concepts/business-case]]
- [[concepts/migration-assessment]]
- [[concepts/schema-conversion]]
- [[concepts/azure-migrate]]
- [[concepts/azure-database-migration-service]]
- [[concepts/sql-server-migration-assistant]]
- [[concepts/azure-sql-database]]
- [[concepts/azure-sql-managed-instance]]
- [[concepts/sql-server-on-azure-vms]]

## Related Sources

- [[sources/azure-database-migration-service-documentation]]
- [[sources/azure-sql-documentation]]

## People

Không có cá nhân cụ thể được nêu bật trong bản trích xuất.

## Open Questions

- Cần tách riêng các hướng dẫn chi tiết cho từng cặp nguồn/đích nếu wiki cần playbook migration thực thi từng bước.

## Notes

