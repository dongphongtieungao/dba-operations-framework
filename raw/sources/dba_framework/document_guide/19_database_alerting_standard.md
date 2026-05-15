---
doc_id: STD-019
title: "Database Alerting Standard"
doc_type: standard
implementation_order: 2
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

# STD-019 - Database Alerting Standard

## 1. Mục đích

Chuẩn hóa cảnh báo database.

## 2. Mô tả tóm tắt

Quy định severity P1 đến P5, ngưỡng cảnh báo, kênh thông báo, escalation, alert owner và mapping mỗi alert với SOP xử lý tương ứng.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Phân loại severity

| Severity | Ý nghĩa | Ví dụ | Kênh thông báo |
|---|---|---|---|
| P1 | Mất dịch vụ hoặc nguy cơ mất dữ liệu nghiêm trọng | Primary down, data corruption, backup chain broken trên production | Gọi điện, incident bridge, ticket khẩn cấp |
| P2 | Suy giảm nghiêm trọng hoặc rủi ro cao | Replication lag vượt RPO, disk log sắp đầy, failover failed | Gọi hoặc chat khẩn, ticket incident |
| P3 | Suy giảm có kiểm soát | Backup failed một lần, slow query ảnh hưởng nhóm nhỏ | Chat, ticket xử lý trong ngày |
| P4 | Cảnh báo cần theo dõi | Capacity trend tăng nhanh, index fragmentation cao | Ticket thông thường |
| P5 | Thông tin hoặc khuyến nghị | Job duration tăng nhẹ, dashboard anomaly | Báo cáo định kỳ |

## 5. Nguyên tắc thiết kế alert

1. Alert phải hành động được, không tạo nhiễu.
2. Mỗi alert phải có owner và SOP hoặc runbook xử lý tương ứng.
3. Alert production phải có threshold khác non production.
4. Alert phải có suppression hoặc maintenance window để tránh cảnh báo giả khi thay đổi có kế hoạch.
5. Alert phải được review định kỳ theo false positive, false negative và incident thực tế.

## 6. Mapping alert với SOP

| Alert | Severity mặc định | SOP liên quan | Runbook liên quan |
|---|---|---|---|
| Backup failed | P2 hoặc P3 | SOP Backup Operation | Runbook Backup Verification |
| Backup overdue | P2 | SOP Backup Operation | Runbook Backup Verification |
| Replication lag vượt ngưỡng | P2 | SOP Replication Troubleshooting | Runbook Database Health Check |
| Primary unavailable | P1 | SOP HA Failover and Switchover | Runbook Failover Execution |
| Data corruption suspected | P1 | SOP Data Corruption Handling | Restore Validation nếu cần |
| Disk usage critical | P2 | SOP Daily Health Check | Runbook Capacity Report |
| Deadlock spike | P3 | SOP Blocking and Deadlock Handling | Health Check |
| Failed login spike | P3 | SOP User and Permission Management | Security evidence |

## 7. Quy tắc escalation

1. P1 phải được acknowledge ngay theo quy định OLA nội bộ.
2. P2 phải có owner xử lý rõ ràng trong ticket.
3. Nếu alert lặp lại 3 lần trong 24 giờ, phải tạo problem ticket hoặc action item.
4. Nếu alert không có SOP xử lý, DBA Lead phải tạo hoặc cập nhật SOP.
5. Nếu alert sai trong nhiều lần, threshold hoặc query alert phải được điều chỉnh.

## 8. Checklist review alert

| Câu hỏi kiểm tra | Kết quả |
|---|---|
| Alert có mô tả rõ tác động không | Pass hoặc Fail |
| Alert có owner không | Pass hoặc Fail |
| Alert có link SOP hoặc runbook không | Pass hoặc Fail |
| Threshold có dựa trên baseline không | Pass hoặc Fail |
| Có kiểm soát maintenance window không | Pass hoặc Fail |
| Có ghi nhận false positive không | Pass hoặc Fail |

## 9. Tài liệu liên quan

1. STD-018 Database Monitoring Standard.
2. SOP-021 Daily Database Health Check.
3. TPL-048 Operation Evidence.

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
