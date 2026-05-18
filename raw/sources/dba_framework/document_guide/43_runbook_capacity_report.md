---
doc_id: DBA-RBK-043
title: "Runbook Capacity Report"
doc_type: runbook
priority: 2
status: draft
owner: DBA Team
reviewer: DBA Lead
approver: Service Owner
version: 0.2
created_date: 2026-05-15
last_updated: 2026-05-18
language: vi
framework: DBA Operations Framework
related_documents:
  - DBA-POL-005 Backup and Restore Policy
  - DBA-TMP-051 Template Monthly DBA Report
  - DBA-RSK-054 DBA Risk Register
---

# DBA-RBK-043 - Runbook Capacity Report

## 1. Mục đích

Tự động hóa báo cáo dung lượng.

## 2. Mô tả tóm tắt

Script tổng hợp database size, growth rate, disk usage, forecast, top growth database và cảnh báo rủi ro hết dung lượng.

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
| report_period | Ngày, tuần hoặc tháng |
| environment | Production, staging, development |
| dbms_type | Loại DBMS hoặc all |
| instance_list | Danh sách instance |
| forecast_window_days | Số ngày dự báo |
| threshold_profile | Ngưỡng capacity |

## 5. Metric cần thu thập

| Metric | Ý nghĩa |
|---|---|
| database_size | Dung lượng database hiện tại |
| data_growth_rate | Tốc độ tăng data |
| log_growth_rate | Tốc độ tăng transaction log hoặc WAL |
| temp_usage_peak | Đỉnh sử dụng temp |
| backup_size | Dung lượng backup |
| disk_free_percent | Phần trăm disk còn trống |
| days_to_threshold | Số ngày dự kiến chạm ngưỡng |
| top_growth_objects | Bảng hoặc database tăng trưởng nhanh nhất |

## 6. Luồng thực thi

1. Thu thập dung lượng hiện tại từ từng instance.
2. Thu thập lịch sử tăng trưởng từ monitoring hoặc repository.
3. Tính growth rate theo ngày và theo tuần.
4. Dự báo thời điểm chạm ngưỡng watch, warning, critical.
5. Xác định top database, top object hoặc top instance rủi ro.
6. Sinh báo cáo capacity.
7. Tạo action item nếu có rủi ro trong forecast window.

## 7. Output báo cáo

| Section | Nội dung |
|---|---|
| Executive summary | Tổng số instance rủi ro, rủi ro cao nhất |
| Top risks | Danh sách database hoặc disk sắp đầy |
| Growth trend | Tăng trưởng theo ngày, tuần, tháng |
| Forecast | Ngày dự kiến chạm ngưỡng |
| Recommended actions | Mở rộng, archive, purge, partition, tune |
| Owner | Người chịu trách nhiệm xử lý |

## 8. Điều kiện tạo ticket

1. Dự báo vượt 80 phần trăm trong 14 ngày.
2. Dự báo vượt 90 phần trăm trong 7 ngày.
3. Transaction log hoặc WAL tăng bất thường.
4. Temp space thường xuyên vượt ngưỡng.
5. Backup storage không đủ retention yêu cầu.

## 9. Tài liệu liên quan

1. STD-020 Capacity Management Standard.
2. TPL-051 Monthly DBA Report.
3. RSK-054 Risk Register.

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
