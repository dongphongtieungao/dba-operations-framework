---
id: azure-sql-documentation
title: Azure SQL documentation
type: source
created: 2026-05-15
updated: 2026-05-15
authors:
  - Microsoft Learn
year: 2026
importance: 4
urls:
  - "https://learn.microsoft.com/azure/azure-sql/"
raw_paths:
  - raw/sources/azure-azure-sql-azuresql.pdf
  - raw/sources/azure-azure-sql-azuresql.txt
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: passed
findings: []
---

## Summary

Tài liệu này là bộ hướng dẫn tổng quan cho họ sản phẩm [[concepts/azure-sql]], gồm [[concepts/azure-sql-database]], [[concepts/azure-sql-managed-instance]] và [[concepts/sql-server-on-azure-vms]]. Trọng tâm của nguồn là giúp DBA chọn đúng mô hình triển khai theo mức độ quản trị mong muốn, mức tương thích SQL Server, yêu cầu mở rộng, chi phí và khả năng hiện đại hóa ứng dụng. Tài liệu cũng gom các chủ đề vận hành quan trọng như bảo mật nâng cao, [[concepts/business-continuity]], kết nối, phát triển ứng dụng, migration và giám sát.

## Key Claims

- Azure SQL là họ sản phẩm dùng SQL Server database engine trên Azure, cho phép chuyển kỹ năng, công cụ và ứng dụng SQL Server hiện có lên cloud với ít thay đổi hơn. (confidence: medium)
- [[concepts/azure-sql-database]] phù hợp cho ứng dụng cloud hiện đại ở mức database, có các lựa chọn như serverless, elastic pools và Hyperscale. (confidence: medium)
- [[concepts/azure-sql-managed-instance]] phù hợp với phần lớn migration SQL Server cần mức tương thích gần SQL Server nhưng vẫn muốn dịch vụ được quản lý. (confidence: medium)
- [[concepts/sql-server-on-azure-vms]] phù hợp khi cần quyền kiểm soát hệ điều hành hoặc tương thích SQL Server đầy đủ theo kiểu lift-and-shift. (confidence: medium)
- Việc chọn dịch vụ nên gắn với yêu cầu quản trị, tương thích, HA/DR, hiệu năng, quy mô dữ liệu và mô hình chi phí thay vì chỉ nhìn vào tên sản phẩm. (confidence: medium)

## Evidence

- Tài liệu liệt kê ba nhóm triển khai chính trong họ Azure SQL và mô tả vai trò của từng nhóm.
- Phần so sánh dịch vụ nhấn mạnh khác biệt PaaS/IaaS, phạm vi quản trị, quyền kiểm soát hệ điều hành, mức tương thích và tình huống migration.
- Các mục liên quan đến bảo mật, business continuity, HA/DR, hiệu năng, kết nối và migration cho thấy tài liệu được tổ chức như một cổng kiến thức vận hành cho DBA.

## Related Concepts

- [[concepts/azure-sql]]
- [[concepts/azure-sql-database]]
- [[concepts/azure-sql-managed-instance]]
- [[concepts/sql-server-on-azure-vms]]
- [[concepts/hyperscale]]
- [[concepts/business-continuity]]
- [[concepts/high-availability-and-disaster-recovery]]
- [[concepts/database-migration]]

## Related Sources

- [[sources/azure-database-migration-guides]]
- [[sources/azure-database-migration-service-documentation]]

## People

Không có cá nhân cụ thể được nêu bật trong bản trích xuất.

## Open Questions

- Bộ tài liệu này rất lớn; trang hiện tại là tổng quan cấp nguồn. Cần tách các trang nguồn hoặc trang chủ đề riêng cho hiệu năng, bảo mật, backup/restore, HA/DR và giới hạn tính năng nếu dùng để ra quyết định triển khai chi tiết.

## Notes
