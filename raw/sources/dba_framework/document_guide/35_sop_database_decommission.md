---
doc_id: SOP-035
title: "SOP Database Decommission"
doc_type: sop
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

# SOP-035 - SOP Database Decommission

## 1. Mục đích

Chuẩn hóa loại bỏ database không còn dùng.

## 2. Mô tả tóm tắt

Hướng dẫn xác nhận owner, kiểm tra dependency, backup cuối, archive, revoke access, disable job, xóa database và cập nhật CMDB.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thực hiện SOP

1. Không thực hiện thao tác production nếu thiếu ticket hoặc approval bắt buộc.
2. Trước khi thực hiện phải xác định rõ phạm vi ảnh hưởng, rollback path và evidence cần lưu.
3. Với thao tác rủi ro cao, cần peer review hoặc DBA Lead review.
4. Sau khi thực hiện phải có post check và cập nhật ticket.
5. Nếu phát sinh sự cố ngoài dự kiến, phải dừng thao tác, bảo toàn evidence và escalate theo severity.


## 4. Khi nào decommission database

1. Ứng dụng đã ngừng hoạt động.
2. Database được thay thế bởi hệ thống mới.
3. Môi trường test hoặc staging hết vòng đời.
4. Database trùng lặp hoặc không còn owner.
5. Database được xác nhận không còn dependency.

## 5. Nguyên tắc decommission

1. Không xóa database nếu chưa xác nhận owner và dependency.
2. Phải có backup cuối hoặc archive theo policy trước khi xóa.
3. Phải revoke quyền truy cập và disable job liên quan.
4. Phải cập nhật inventory, CMDB, monitoring và alert.
5. Phải có thời gian quarantine nếu rủi ro dependency chưa rõ.

## 6. Quy trình thực hiện

1. Tiếp nhận yêu cầu decommission.
2. Xác nhận service owner và business owner.
3. Kiểm tra dependency từ app, job, report, ETL, replication, linked server, backup và monitoring.
4. Thông báo kế hoạch decommission cho các bên liên quan.
5. Chuyển database sang trạng thái read only hoặc restricted nếu phù hợp.
6. Tạo backup cuối hoặc archive.
7. Disable job, revoke user, tắt alert và remove khỏi monitoring sau khi được phê duyệt.
8. Xóa database hoặc giữ archive theo retention.
9. Cập nhật CMDB và risk register nếu còn rủi ro.

## 7. Checklist dependency

| Dependency | Cách kiểm tra |
|---|---|
| Application | Connection string, config, secret, traffic |
| Job | Scheduler, SQL Agent, cron, Airflow, batch |
| Report | BI report, dashboard, export job |
| ETL | Source hoặc target pipeline |
| Replication | Publisher, subscriber, replica, CDC |
| User | Login gần nhất, privileged access |
| Backup | Retention, archive, restore requirement |
| Monitoring | Alert, dashboard, inventory |

## 8. Evidence bắt buộc

1. Approval của service owner.
2. Dependency check result.
3. Backup cuối hoặc archive evidence.
4. Danh sách quyền đã revoke.
5. Danh sách job đã disable.
6. CMDB hoặc inventory update evidence.

## 9. Tài liệu liên quan

1. POL-006 Data Lifecycle Management Policy.
2. POL-010 Backup and Restore Policy.
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
