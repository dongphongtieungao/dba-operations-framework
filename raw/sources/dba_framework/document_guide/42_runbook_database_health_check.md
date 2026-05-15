---
doc_id: RBK-042
title: "Runbook Database Health Check"
doc_type: runbook
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

# RBK-042 - Runbook Database Health Check

## 1. Mục đích

Tự động hóa health check định kỳ.

## 2. Mô tả tóm tắt

Script thu thập metric, trạng thái instance, disk, connection, replication, backup, error log và xuất báo cáo ngắn.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thiết kế runbook

1. Runbook phải idempotent ở mức có thể, hoặc phải kiểm tra trạng thái trước khi thay đổi.
2. Không hard code credential, secret hoặc thông tin nhạy cảm trong script.
3. Mọi input phải được validate trước khi thực thi.
4. Script phải có chế độ dry run nếu thao tác có rủi ro cao.
5. Script phải xuất output có cấu trúc để lưu evidence và tích hợp ITSM.
6. Thao tác production phải dùng service account được phê duyệt.


## 4. Input bắt buộc

| Input | Mô tả |
|---|---|
| execution_id | Mã lần chạy health check |
| environment | Môi trường kiểm tra |
| dbms_type | Loại DBMS |
| instance_list | Danh sách instance cần kiểm tra |
| output_format | Markdown, JSON, CSV hoặc ticket attachment |
| threshold_profile | Bộ ngưỡng áp dụng |

## 5. Nhóm kiểm tra tối thiểu

| Nhóm | Nội dung |
|---|---|
| Instance | Up, role, uptime, restart gần nhất |
| Disk | Data, log, temp, backup storage |
| Connection | Active, idle, max usage, failed connection |
| Backup | Last backup, backup age, failed backup |
| Replication | Lag, sync state, replica health |
| Error log | Lỗi nghiêm trọng, corruption, crash, failed login |
| Blocking | Blocking session, deadlock, lock wait |
| Alert | Alert tồn đọng chưa xử lý |

## 6. Luồng thực thi

1. Load inventory database.
2. Load threshold profile theo môi trường.
3. Kết nối từng instance bằng service account read only.
4. Thu thập metric và log summary.
5. Chuẩn hóa kết quả thành PASS, WARN, FAIL.
6. Sinh báo cáo ngắn.
7. Gửi cảnh báo nếu có FAIL hoặc WARN nghiêm trọng.
8. Lưu artifact và cập nhật ticket định kỳ nếu có.

## 7. Output báo cáo

| Trường | Mô tả |
|---|---|
| checked_at | Thời điểm kiểm tra |
| instance_name | Instance được kiểm tra |
| dbms_type | Loại DBMS |
| overall_status | PASS, WARN, FAIL |
| backup_status | Trạng thái backup |
| replication_status | Trạng thái replication |
| capacity_status | Trạng thái dung lượng |
| critical_findings | Danh sách phát hiện quan trọng |
| recommended_action | Hành động khuyến nghị |

## 8. Điều kiện fail

1. Instance production không truy cập được.
2. Backup vượt ngưỡng RPO.
3. Replication lag vượt ngưỡng RPO.
4. Disk data, log hoặc temp vượt ngưỡng critical.
5. Có lỗi corruption trong log.
6. Có alert P1 hoặc P2 chưa được acknowledge.

## 9. Tài liệu liên quan

1. SOP-021 Daily Database Health Check.
2. STD-018 Monitoring Standard.
3. STD-019 Alerting Standard.

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
