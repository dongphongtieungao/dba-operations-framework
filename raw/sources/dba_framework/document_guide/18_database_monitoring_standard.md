---
doc_id: STD-018
title: "Database Monitoring Standard"
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

# STD-018 - Database Monitoring Standard

## 1. Mục đích

Chuẩn hóa quan sát database.

## 2. Mô tả tóm tắt

Định nghĩa golden signals cho database, metric bắt buộc, dashboard tối thiểu, log cần thu thập, correlation với app, infra và ticket.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Golden signals cho database

| Golden signal | Ý nghĩa với database | Ví dụ metric |
|---|---|---|
| Latency | Thời gian phản hồi truy vấn hoặc transaction | query duration, commit latency, lock wait time |
| Traffic | Lưu lượng truy cập database | connection count, transactions per second, queries per second |
| Errors | Lỗi vận hành hoặc lỗi truy vấn | failed login, deadlock, backup failed, replication error |
| Saturation | Mức độ cạn kiệt tài nguyên | CPU, memory, IOPS, disk, temp space, log usage |

## 5. Metric bắt buộc

| Nhóm metric | Metric tối thiểu |
|---|---|
| Availability | instance up, database online, endpoint reachable |
| Resource | CPU, memory, disk usage, IOPS, network throughput |
| Connection | active connection, max connection usage, connection failure |
| Transaction | TPS, commit latency, rollback count, log generation rate |
| Lock | blocking session, lock wait, deadlock count |
| Query | slow query, top SQL by duration, top SQL by IO, plan regression |
| Backup | last successful backup, backup age, backup failed count |
| Replication | replication lag, replica health, sync status |
| Storage | data size, log size, temp usage, growth rate |
| Security | failed login, privileged access, permission change |

## 6. Dashboard tối thiểu

1. Executive health dashboard.
2. Instance detail dashboard.
3. Backup and restore readiness dashboard.
4. HA and replication dashboard.
5. Capacity dashboard.
6. Query performance dashboard.
7. Security and audit dashboard.

## 7. Correlation với app, infra và ticket

1. Metric database phải có tag environment, service, application, dbms, instance và owner.
2. Alert phải liên kết được với ticket hoặc incident ID.
3. Slow query cần correlation với application endpoint hoặc job nếu có dữ liệu tracing.
4. Resource saturation cần correlation với host, VM, storage, Kubernetes hoặc cloud metric.
5. Dashboard vận hành nên hiển thị change window gần nhất để phát hiện regression sau thay đổi.

## 8. Log cần thu thập

| Nhóm log | Nội dung |
|---|---|
| Error log | Lỗi instance, crash, restart, corruption, backup failed |
| Audit log | Login, permission change, schema change, privileged operation |
| Slow query log | Query chậm, duration, rows, plan hoặc hash nếu có |
| Replication log | Lag, disconnect, conflict, failover event |
| Backup log | Backup status, duration, size, destination, error |
| Job log | Job success, failed, duration, retry |

## 9. Tài liệu liên quan

1. STD-019 Database Alerting Standard.
2. STD-020 Capacity Management Standard.
3. SOP-021 Daily Database Health Check.

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
