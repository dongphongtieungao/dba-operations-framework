---
doc_id: DBA-RBK-043
title: "Runbook Capacity Report"
doc_type: runbook
priority: 2
status: ready_for_review
owner: DBA Team
reviewer: DBA Lead
approver: Service Owner
version: 1.0
created_date: 2026-05-15
last_updated: 2026-05-18
language: vi
framework: DBA Operations Framework
related_documents:
  - DBA-STD-020 Database Capacity Management Standard
  - DBA-POL-005 Backup and Restore Policy
  - DBA-TMP-051 Template Monthly DBA Report
  - DBA-RSK-054 DBA Risk Register
---

# DBA-RBK-043 - Runbook Capacity Report

## 1. Mục đích

Tự động hóa báo cáo dung lượng database estate nhằm phát hiện sớm rủi ro hết dung lượng, tăng trưởng bất thường, thiếu backup storage, áp lực compute và chi phí vượt kỳ vọng.

## 2. Mô tả tóm tắt

Script tổng hợp database size, growth rate, disk/storage usage, log/temp usage, backup size, compute utilization, forecast, top growth database/object và cảnh báo rủi ro hết dung lượng. Báo cáo phải tạo action item có owner khi forecast vượt ngưỡng.

## 3. Phạm vi áp dụng

Áp dụng cho production, staging và các môi trường non-production quan trọng. Với SQL Server, báo cáo cần bao gồm data/log/tempdb/backup storage. Với Azure SQL, báo cáo cần bao gồm storage max size, vCore/DTU, elastic pool, Hyperscale hoặc serverless signal nếu dùng.

## 4. Nguyên tắc thiết kế runbook

1. Capacity report phải dựa trên dữ liệu hiện tại và lịch sử tăng trưởng, không chỉ snapshot.
2. Forecast phải nêu số ngày chạm ngưỡng watch, warning, critical.
3. Không hard code credential, secret hoặc thông tin nhạy cảm trong script.
4. Script phải xuất output có cấu trúc để lưu evidence và tích hợp ITSM.
5. Mọi risk trong forecast window phải có owner hoặc action item.
6. Báo cáo capacity phải liên kết với risk register và monthly DBA report.

## 5. Input bắt buộc

| Input | Mô tả |
|---|---|
| report_period | Ngày, tuần hoặc tháng |
| environment | Production, staging, development |
| dbms_type | Loại DBMS hoặc all |
| instance_list | Danh sách instance |
| database_scope | All hoặc danh sách database |
| forecast_window_days | Số ngày dự báo |
| threshold_profile | Ngưỡng capacity |
| history_window_days | Số ngày lịch sử dùng để tính trend |
| output_format | Markdown, JSON, CSV hoặc ticket attachment |

## 6. Metric cần thu thập

| Metric | Ý nghĩa |
|---|---|
| database_size | Dung lượng database hiện tại |
| allocated_size | Dung lượng đã cấp phát |
| data_growth_rate | Tốc độ tăng data |
| log_growth_rate | Tốc độ tăng transaction log hoặc WAL |
| temp_usage_peak | Đỉnh sử dụng temp/tempdb |
| backup_size | Dung lượng backup |
| backup_storage_free_percent | Phần trăm backup storage còn trống |
| disk_free_percent | Phần trăm disk còn trống |
| max_size_or_quota | Giới hạn storage, quota hoặc max size |
| compute_utilization | CPU, DTU, vCore hoặc pool utilization |
| days_to_threshold | Số ngày dự kiến chạm ngưỡng |
| top_growth_objects | Bảng hoặc database tăng trưởng nhanh nhất |

## 7. Luồng thực thi

1. Thu thập dung lượng hiện tại từ từng instance/database.
2. Thu thập lịch sử tăng trưởng từ monitoring hoặc repository.
3. Chuẩn hóa đơn vị đo và loại bỏ dữ liệu thiếu rõ ràng.
4. Tính growth rate theo ngày, tuần và kỳ báo cáo.
5. Dự báo thời điểm chạm ngưỡng watch, warning, critical.
6. Xác định top database, top object hoặc top instance rủi ro.
7. Phân loại risk theo impact và urgency.
8. Sinh báo cáo capacity.
9. Tạo action item nếu có rủi ro trong forecast window.
10. Cập nhật monthly DBA report hoặc risk register nếu cần.

## 8. Output báo cáo

| Section | Nội dung |
|---|---|
| Executive summary | Tổng số instance/database rủi ro, rủi ro cao nhất |
| Coverage | Số lượng instance/database có dữ liệu đầy đủ |
| Top risks | Danh sách database, disk, pool hoặc storage sắp đầy |
| Growth trend | Tăng trưởng theo ngày, tuần, tháng |
| Forecast | Ngày dự kiến chạm ngưỡng |
| Cost signal | SKU, compute hoặc storage tăng bất thường nếu có |
| Recommended actions | Mở rộng, archive, purge, partition, tune |
| Owner | Người chịu trách nhiệm xử lý |
| Evidence | Link dashboard, export, raw output hoặc ticket |

## 9. Điều kiện tạo ticket

1. Dự báo vượt 80 phần trăm trong 14 ngày.
2. Dự báo vượt 90 phần trăm trong 7 ngày.
3. Transaction log hoặc WAL tăng bất thường.
4. Temp space thường xuyên vượt ngưỡng.
5. Backup storage không đủ retention yêu cầu.
6. Elastic pool hoặc compute pool có dấu hiệu bị một database chiếm dụng.
7. Cost hoặc SKU tăng không khớp với workload đã biết.

## 10. Gợi ý kiểm tra theo nền tảng

### 10.1. SQL Server

1. Data file size, log file size, used/free space và autogrowth event.
2. Tempdb size, peak usage và contention signal nếu có.
3. Backup size, backup retention và backup storage.
4. Top table/index growth, heap hoặc index bất thường.
5. Disk free percent theo drive data/log/tempdb/backup.

### 10.2. Azure SQL Database

1. Storage used, allocated, max size và days to max.
2. DTU/vCore utilization, CPU, data IO, log IO.
3. Elastic pool utilization và noisy database.
4. Hyperscale hoặc serverless behavior nếu áp dụng.
5. SKU/cost signal và khả năng rightsizing.

### 10.3. Azure SQL Managed Instance

1. Instance storage, database size, tempdb và log growth.
2. vCore utilization, storage IO và service tier.
3. SQL Agent job hoặc automation liên quan đến purge/archive.

## 11. Recommended action catalog

| Tình huống | Hành động khuyến nghị |
|---|---|
| Data growth đều và dự báo chạm ngưỡng | Mở rộng storage hoặc tăng max size theo approval |
| Log growth bất thường | Kiểm tra transaction dài, backup log, batch job hoặc replication |
| Temp usage cao | Kiểm tra query, sort/hash spill, index operation hoặc ETL |
| Backup storage thiếu | Tăng storage, điều chỉnh retention hoặc archive theo policy |
| Elastic pool bị áp lực | Tách database gây tải, tăng pool hoặc đặt cap phù hợp |
| Object tăng bất thường | Kiểm tra purge/archive, partition hoặc lỗi ứng dụng |

## 12. Tài liệu liên quan

1. STD-020 Capacity Management Standard.
2. TPL-051 Monthly DBA Report.
3. RSK-054 Risk Register.
4. RBK-042 Database Health Check.

## 13. Yêu cầu evidence

Mọi hoạt động phát sinh từ tài liệu này cần để lại evidence tối thiểu sau:

1. Ticket ID hoặc change ID nếu có.
2. Người yêu cầu, người thực hiện, người phê duyệt nếu có.
3. Thời gian bắt đầu và kết thúc.
4. Input đã sử dụng.
5. Output hoặc log thao tác.
6. Kết quả kiểm tra sau thực hiện.
7. Đường dẫn dashboard, report, script output hoặc artifact liên quan.
8. Forecast table và danh sách action item.

## 14. Tiêu chí nghiệm thu

1. Nội dung được áp dụng nhất quán cho các database thuộc phạm vi quản lý.
2. Có evidence chứng minh việc thực hiện đúng quy trình.
3. Có owner chịu trách nhiệm duy trì tài liệu.
4. Có chu kỳ review định kỳ hoặc review sau sự cố lớn.
5. Có liên kết đến policy, SOP, runbook hoặc template liên quan.
6. Báo cáo chỉ rõ top risk, ngày dự kiến chạm ngưỡng và owner xử lý.

## 15. Chỉ số liên quan

| KPI | Công thức tham khảo |
|---|---|
| Capacity report coverage | Databases reported / Databases in inventory |
| Forecast accuracy | Forecasted threshold date vs actual threshold date |
| Capacity risk count | Number of active capacity risks |
| Time to mitigation | Mitigation completed date - Risk detected date |
| Storage utilization compliance | Databases below threshold / Total databases |

## 16. Quản trị thay đổi tài liệu

Tài liệu phải được review ít nhất mỗi 6 tháng hoặc sau các sự kiện sau:

1. Có sự cố P1 hoặc P2 liên quan.
2. Có thay đổi lớn về kiến trúc database.
3. Có thay đổi về yêu cầu bảo mật, audit hoặc compliance.
4. Có thay đổi công cụ vận hành hoặc nền tảng cloud.
5. Có phát hiện sai lệch giữa tài liệu và thực tế vận hành.
