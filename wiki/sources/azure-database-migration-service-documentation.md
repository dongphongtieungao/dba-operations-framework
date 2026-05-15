---
id: azure-database-migration-service-documentation
title: Azure Database Migration Service documentation
type: source
created: 2026-05-15
updated: 2026-05-15
authors:
  - Microsoft Learn
year: 2026
importance: 4
urls:
  - "https://learn.microsoft.com/azure/dms/"
raw_paths:
  - raw/sources/azure-dms.pdf
  - raw/sources/azure-dms.txt
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: passed
findings: []
---

## Summary

Tài liệu này mô tả [[concepts/azure-database-migration-service]] như một dịch vụ được quản lý để di chuyển cơ sở dữ liệu từ nhiều nguồn sang nền tảng dữ liệu Azure với downtime tối thiểu khi dùng migration online. Với SQL Server, tài liệu nhấn mạnh các đường đi đến [[concepts/azure-sql-database]], [[concepts/azure-sql-managed-instance]] và [[concepts/sql-server-on-azure-vms]], đồng thời liên kết DMS với đánh giá readiness của Azure Arc và các công cụ như [[concepts/azure-migrate]] và [[concepts/sql-server-migration-assistant]]. Nguồn này hữu ích để DBA hiểu bước thực thi migration, quyền cần cấp, chế độ online/offline và các giới hạn cần kiểm tra trước khi cutover.

## Key Claims

- [[concepts/azure-database-migration-service]] là dịch vụ được quản lý, có thể tạo và theo dõi migration qua Azure portal, PowerShell và Azure CLI. (confidence: medium)
- DMS hỗ trợ migration offline tới [[concepts/azure-sql-database]], online/offline tới [[concepts/azure-sql-managed-instance]], và online/offline tới [[concepts/sql-server-on-azure-vms]]. (confidence: medium)
- DMS dùng công nghệ đánh giá readiness liên quan Azure Arc để đưa ra khuyến nghị trước migration, nhưng DBA vẫn phải xử lý remediation trước khi chạy migration. (confidence: medium)
- DMS hỗ trợ tự động hóa, private endpoints và migration schema, nhưng tài liệu nêu một số khả năng không nằm trong DMS portal như migration logins hoặc TDE theo bảng so sánh được trích xuất. (confidence: low)
- Các migration thực tế cần kiểm tra quyền tùy chỉnh, trạng thái kịch bản, known issues và khả năng khu vực trước khi triển khai. (confidence: medium)

## Evidence

- Phần "What is Azure Database Migration Service?" mô tả DMS là fully managed service cho migration online với downtime tối thiểu.
- Bảng chức năng trong tài liệu liệt kê các target SQL Server/Azure SQL và phân biệt online/offline.
- Tài liệu có các mục riêng về custom roles cho Azure SQL Database, Azure SQL Managed Instance và SQL Server on Azure VMs.
- Các phần known issues cho PostgreSQL, MySQL và MongoDB cho thấy migration ngoài SQL Server cũng cần kiểm tra giới hạn riêng theo nền tảng.

## Related Concepts

- [[concepts/azure-database-migration-service]]
- [[concepts/database-migration]]
- [[concepts/online-and-offline-migration]]
- [[concepts/migration-assessment]]
- [[concepts/azure-migrate]]
- [[concepts/sql-server-migration-assistant]]
- [[concepts/private-endpoints]]
- [[concepts/transparent-data-encryption]]
- [[concepts/azure-sql-database]]
- [[concepts/azure-sql-managed-instance]]
- [[concepts/sql-server-on-azure-vms]]

## Related Sources

- [[sources/azure-database-migration-guides]]
- [[sources/azure-sql-documentation]]

## People

Không có cá nhân cụ thể được nêu bật trong bản trích xuất.

## Open Questions

- Cần đối chiếu thêm phần mới nhất của Microsoft Learn trước khi dùng các dòng "không hỗ trợ" trong bảng chức năng làm quyết định cuối cùng, vì tính năng dịch vụ có thể thay đổi.

## Notes

