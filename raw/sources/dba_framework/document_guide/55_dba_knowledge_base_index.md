---
doc_id: IDX-055
title: "DBA Knowledge Base Index"
doc_type: index
implementation_order: 5
status: draft
owner: DBA Team
reviewer: DBA Lead
approver: Service Owner
version: 0.1
created_date: 2026-05-15
last_updated: 2026-05-15
language: vi
framework: DBA Operations Framework
---

# IDX-055 - DBA Knowledge Base Index

## 1. Mục đích

Làm cổng tra cứu tri thức DBA.

## 2. Mô tả tóm tắt

Tài liệu map toàn bộ policy, standard, SOP, runbook, template, report theo chủ đề để DBA và AI agent có thể tìm kiếm nhanh.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Mục tiêu của Knowledge Base Index

1. Là cổng tra cứu chính cho toàn bộ tài liệu DBA Operations Framework.
2. Giúp DBA tìm đúng policy, SOP, runbook hoặc template theo tình huống vận hành.
3. Giúp AI agent dùng `rg` tìm nhanh tài liệu liên quan mà không phải đọc toàn bộ KMS.
4. Giảm rủi ro tài liệu trùng lặp, mâu thuẫn hoặc lỗi thời.

## 5. Cấu trúc index theo nhóm tài liệu

| Nhóm | Phạm vi | Ví dụ tài liệu |
|---|---|---|
| Overview | Định nghĩa framework và cách dùng | DBA Operations Framework Overview |
| Policy | Nguyên tắc bắt buộc | Backup and Restore Policy, Security Policy |
| Standard | Chuẩn thiết kế và vận hành | Monitoring Standard, SQL Coding Standard |
| SOP | Quy trình thao tác | Backup Operation, Restore Operation, Failover |
| Runbook | Tự động hóa | Backup Verification, Health Check, Capacity Report |
| Template | Form và evidence | Operation Evidence, RFC, PIR |
| Metrics | KPI và đo lường | DBA KPI and Metrics Catalog |
| Appendix | Đặc thù DBMS | SQL Server Appendix, Azure SQL Appendix |

## 6. Index theo tình huống vận hành

| Tình huống | Tài liệu cần đọc trước |
|---|---|
| Backup failed | Backup and Restore Policy, SOP Backup Operation, Runbook Backup Verification, Operation Evidence |
| Cần restore dữ liệu | Backup and Restore Policy, SOP Restore Operation, Restore Validation, Restore Drill Report |
| Cần failover | HA and DR Policy, SOP HA Failover and Switchover, Runbook Failover Execution, Failover Drill Report |
| Query chậm | SQL Coding Standard, Indexing Standard, SOP Query Tuning Review, SOP Performance Troubleshooting |
| Cấp quyền | Access Control Policy, SOP User and Permission Management, Runbook User Provisioning |
| Schema change | Change Management Policy, Schema Design Standard, SOP Schema Change Review, RFC Template |
| Capacity risk | Capacity Management Standard, Runbook Capacity Report, Risk Register, Monthly DBA Report |
| Data corruption | Backup and Restore Policy, SOP Data Corruption Handling, Restore Validation, PIR Template |

## 7. Quy ước tag cho Obsidian hoặc KMS

| Tag | Ý nghĩa |
|---|---|
| dba | Tài liệu thuộc DBA framework |
| policy | Policy bắt buộc |
| standard | Chuẩn thiết kế hoặc vận hành |
| sop | Quy trình thao tác |
| runbook | Script hoặc playbook tự động hóa |
| evidence | Tài liệu liên quan bằng chứng |
| backup | Chủ đề backup |
| restore | Chủ đề restore |
| ha-dr | Chủ đề HA hoặc DR |
| security | Chủ đề bảo mật |
| performance | Chủ đề hiệu năng |
| capacity | Chủ đề dung lượng |

## 8. Gợi ý tìm kiếm bằng rg

```bash
rg -n "Backup and Restore|backup failed|restore validation" docs/
rg -n "failover|switchover|replication lag|RTO|RPO" docs/
rg -n "query tuning|execution plan|index|statistics" docs/
rg -n "access control|least privilege|grant|revoke" docs/
rg -n "capacity|growth rate|disk usage|forecast" docs/
```

## 9. Quy tắc duy trì index

1. Mỗi tài liệu mới phải được thêm vào index.
2. Mỗi SOP phải link đến policy, runbook và template liên quan.
3. Mỗi runbook phải link đến SOP liên quan.
4. Tài liệu deprecated phải được đánh dấu rõ và không dùng làm nguồn vận hành chính.
5. Index phải được review khi có thay đổi lớn trong taxonomy hoặc service catalog.

## 9. Tài liệu liên quan

1. All DBA Operations Framework documents.

## 10. Yêu cầu evidence

Mọi hoạt động phát sinh từ tài liệu này cần để lại evidence tối thiểu sau:

1. Ticket ID hoặc change ID.
2. Người yêu cầu, người thực hiện, người phê duyệt nếu có.
3. Thời gian bắt đầu và kết thúc.
4. Input đã sử dụng.
5. Output hoặc log thao tác.
6. Kết quả kiểm tra sau thực hiện.
7. Đường dẫn dashboard, report, script output hoặc artifact liên quan.

## 11. Tiêu chí nghiệm thu

1. Nội dung được áp dụng nhất quán cho các database thuộc phạm vi quản lý.
2. Có evidence chứng minh việc thực hiện đúng quy trình.
3. Có owner chịu trách nhiệm duy trì tài liệu.
4. Có chu kỳ review định kỳ hoặc review sau sự cố lớn.
5. Có liên kết đến policy, SOP, runbook hoặc template liên quan.

## 12. Quản trị thay đổi tài liệu

Tài liệu phải được review ít nhất mỗi 6 tháng hoặc sau các sự kiện sau:

1. Có sự cố P1 hoặc P2 liên quan.
2. Có thay đổi lớn về kiến trúc database.
3. Có thay đổi về yêu cầu bảo mật, audit hoặc compliance.
4. Có thay đổi công cụ vận hành hoặc nền tảng cloud.
5. Có phát hiện sai lệch giữa tài liệu và thực tế vận hành.
