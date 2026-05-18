---
doc_id: RBK-042
title: "Runbook Database Health Check"
doc_type: runbook
implementation_order: 2
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
  - DBA-SOP-021 SOP Daily Database Health Check
  - DBA-STD-018 Database Monitoring Standard
  - DBA-STD-019 Database Alerting Standard
  - DBA-RBK-038 Runbook Backup Verification
  - DBA-RBK-043 Runbook Capacity Report
  - DBA-TMP-048 Template Operation Evidence
---

# RBK-042 - Runbook Database Health Check

## 1. Mục đích

Tự động hóa health check định kỳ để phát hiện sớm rủi ro về availability, backup, replication, capacity, connection, error log, blocking, security signal và alert tồn đọng trước khi ảnh hưởng dịch vụ.

## 2. Mô tả tóm tắt

Script thu thập metric, trạng thái instance, disk/storage, connection, replication, backup, error log, blocking/deadlock, job, security signal và alert; sau đó chuẩn hóa kết quả thành PASS/WARN/FAIL, sinh báo cáo ngắn, tạo ticket nếu vượt ngưỡng và lưu evidence.

## 3. Phạm vi áp dụng

Áp dụng cho production, staging và các môi trường non-production quan trọng. Khi triển khai đa DBMS, nội dung này là baseline chung; khác biệt riêng của SQL Server, Azure SQL, PostgreSQL, MySQL/MariaDB, Oracle hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.

## 4. Nguyên tắc thiết kế runbook

1. Health check phải đọc trạng thái, không thay đổi cấu hình nếu không có chế độ xử lý riêng được phê duyệt.
2. Không hard code credential, secret hoặc thông tin nhạy cảm trong script.
3. Mọi input phải được validate trước khi thực thi.
4. Script phải xuất output có cấu trúc để lưu evidence và tích hợp ITSM.
5. Thao tác production phải dùng service account read-only hoặc quyền tối thiểu.
6. Kết quả FAIL phải có owner xử lý, ticket hoặc incident.

## 5. Input bắt buộc

| Input | Mô tả |
|---|---|
| execution_id | Mã lần chạy health check |
| environment | Môi trường kiểm tra |
| dbms_type | Loại DBMS |
| instance_list | Danh sách instance cần kiểm tra |
| database_scope | All hoặc danh sách database |
| output_format | Markdown, JSON, CSV hoặc ticket attachment |
| threshold_profile | Bộ ngưỡng áp dụng |
| report_date | Ngày báo cáo |
| evidence_path | Nơi lưu artifact |

## 6. Nhóm kiểm tra tối thiểu

| Nhóm | Nội dung |
|---|---|
| Instance | Up, role, uptime, restart gần nhất, version/build |
| Database | Online, read/write mode, suspect/recovery state |
| Disk/Storage | Data, log, temp, backup storage, free percent |
| Connection | Active, idle, max usage, failed connection |
| Backup | Last backup, backup age, failed backup, chain status |
| Replication/HA | Lag, sync state, replica health, listener/failover group |
| Error log | Lỗi nghiêm trọng, corruption, crash, failed login |
| Blocking | Blocking session, deadlock, lock wait |
| Job/Automation | Failed job, job chạy lâu, scheduler failure |
| Security | Privileged role drift, failed login spike, audit finding |
| Alert | Alert tồn đọng chưa xử lý |

## 7. Luồng thực thi

1. Load inventory database.
2. Load threshold profile theo môi trường.
3. Kết nối từng instance bằng service account read-only.
4. Thu thập metric, log summary và trạng thái alert.
5. Chuẩn hóa kết quả từng nhóm thành PASS, WARN, FAIL.
6. Xác định critical findings và recommended action.
7. Sinh báo cáo ngắn.
8. Gửi cảnh báo nếu có FAIL hoặc WARN nghiêm trọng.
9. Tạo ticket nếu điều kiện tạo ticket được kích hoạt.
10. Lưu artifact và cập nhật ticket định kỳ nếu có.

## 8. Output báo cáo

| Trường | Mô tả |
|---|---|
| checked_at | Thời điểm kiểm tra |
| instance_name | Instance được kiểm tra |
| database_name | Database nếu kiểm tra ở cấp database |
| dbms_type | Loại DBMS |
| overall_status | PASS, WARN, FAIL |
| backup_status | Trạng thái backup |
| replication_status | Trạng thái replication |
| capacity_status | Trạng thái dung lượng |
| performance_status | Blocking, deadlock, waits, query regression nếu có |
| security_status | Failed login, privileged access, audit finding |
| critical_findings | Danh sách phát hiện quan trọng |
| recommended_action | Hành động khuyến nghị |
| evidence_path | Đường dẫn evidence |

## 9. Điều kiện fail

1. Instance production không truy cập được.
2. Database production offline, suspect hoặc recovery pending.
3. Backup vượt ngưỡng RPO.
4. Replication lag vượt ngưỡng RPO.
5. Disk data, log, temp hoặc backup storage vượt ngưỡng critical.
6. Có lỗi corruption trong log.
7. Có blocking hoặc deadlock nghiêm trọng ảnh hưởng dịch vụ.
8. Có alert P1 hoặc P2 chưa được acknowledge.
9. Monitoring không nhận dữ liệu từ instance production.

## 10. Gợi ý kiểm tra theo nền tảng

### 10.1. SQL Server

1. SQL Server service/instance uptime, version và recent restart.
2. Database state, recovery model, log reuse wait và suspect pages.
3. SQL Agent failed jobs, backup job, CHECKDB job.
4. Always On AG health, listener, synchronization state, log send queue, redo queue.
5. Wait stats, blocking, deadlock, Query Store top regression.
6. Data/log/tempdb size, autogrowth event và backup storage.

### 10.2. Azure SQL

1. Database status, service tier, DTU/vCore, CPU, data IO, log IO.
2. Storage used/max size, elastic pool usage nếu có.
3. Connection failure, deadlock, throttling hoặc resource limit signal.
4. Query Store, automatic tuning setting và top resource consumers.
5. Diagnostic settings, Azure Monitor alert và failover group/geo-replication state.
6. Firewall/private endpoint hoặc connectivity signal.

## 11. Điều kiện tạo ticket

1. Bất kỳ FAIL nào trên production.
2. WARN lặp lại quá 2 lần liên tiếp trên cùng database hoặc instance.
3. Backup hoặc replication gần vượt RPO.
4. Capacity dự báo chạm ngưỡng critical trong forecast window.
5. Alert P1/P2 chưa có owner xử lý.
6. Monitoring mất dữ liệu hoặc không chạy trong kỳ kiểm tra.

## 12. Tài liệu liên quan

1. SOP-021 Daily Database Health Check.
2. STD-018 Monitoring Standard.
3. STD-019 Alerting Standard.
4. RBK-038 Backup Verification.
5. RBK-043 Capacity Report.

## 13. Yêu cầu evidence

Mọi hoạt động phát sinh từ tài liệu này cần để lại evidence tối thiểu sau:

1. Ticket ID hoặc change ID nếu có.
2. Người yêu cầu, người thực hiện, người phê duyệt nếu có.
3. Thời gian bắt đầu và kết thúc.
4. Input đã sử dụng.
5. Output hoặc log thao tác.
6. Kết quả kiểm tra sau thực hiện.
7. Đường dẫn dashboard, report, script output hoặc artifact liên quan.
8. Danh sách FAIL/WARN và owner xử lý.

## 14. Tiêu chí nghiệm thu

1. Nội dung được áp dụng nhất quán cho các database thuộc phạm vi quản lý.
2. Có evidence chứng minh việc thực hiện đúng quy trình.
3. Có owner chịu trách nhiệm duy trì tài liệu.
4. Có chu kỳ review định kỳ hoặc review sau sự cố lớn.
5. Có liên kết đến policy, SOP, runbook hoặc template liên quan.
6. Báo cáo health check có thể được dùng để mở ticket xử lý ngay.

## 15. Chỉ số liên quan

| KPI | Công thức tham khảo |
|---|---|
| Health check coverage | Instances checked / Instances in inventory |
| Health check pass rate | PASS checks / Total checks |
| Critical finding count | Number of FAIL findings |
| Repeat warning rate | Repeated WARN findings / Total WARN findings |
| Mean time to acknowledge | Alert acknowledged time - Alert raised time |

## 16. Quản trị thay đổi tài liệu

Tài liệu phải được review ít nhất mỗi 6 tháng hoặc sau các sự kiện sau:

1. Có sự cố P1 hoặc P2 liên quan.
2. Có thay đổi lớn về kiến trúc database.
3. Có thay đổi về yêu cầu bảo mật, audit hoặc compliance.
4. Có thay đổi công cụ vận hành hoặc nền tảng cloud.
5. Có phát hiện sai lệch giữa tài liệu và thực tế vận hành.
