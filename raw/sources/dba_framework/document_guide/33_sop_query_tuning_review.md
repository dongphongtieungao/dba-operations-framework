---
doc_id: SOP-033
title: "SOP Query Tuning Review"
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

# SOP-033 - SOP Query Tuning Review

## 1. Mục đích

Chuẩn hóa review truy vấn chậm.

## 2. Mô tả tóm tắt

Hướng dẫn thu thập query, plan, runtime metric, cardinality, index usage, wait event, đề xuất index hoặc rewrite query và xác nhận before after.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thực hiện SOP

1. Không thực hiện thao tác production nếu thiếu ticket hoặc approval bắt buộc.
2. Trước khi thực hiện phải xác định rõ phạm vi ảnh hưởng, rollback path và evidence cần lưu.
3. Với thao tác rủi ro cao, cần peer review hoặc DBA Lead review.
4. Sau khi thực hiện phải có post check và cập nhật ticket.
5. Nếu phát sinh sự cố ngoài dự kiến, phải dừng thao tác, bảo toàn evidence và escalate theo severity.


## 4. Khi nào cần query tuning review

1. Query vượt ngưỡng SLA hoặc SLO.
2. Query gây CPU, IO, memory hoặc temp usage cao.
3. Query gây blocking hoặc deadlock.
4. Query có plan regression sau release hoặc thống kê thay đổi.
5. Job batch chạy quá cửa sổ vận hành.

## 5. Dữ liệu cần thu thập

| Dữ liệu | Mục đích |
|---|---|
| SQL text hoặc query hash | Xác định truy vấn cần phân tích |
| Execution plan | Xem scan, join, sort, lookup, estimate |
| Runtime metric | Duration, CPU, logical read, physical read, row count |
| Wait hoặc lock | Xác định nghẽn tài nguyên |
| Index hiện có | Đánh giá thiếu hoặc dư index |
| Statistics | Đánh giá cardinality estimate |
| Tần suất chạy | Phân biệt query hot path và query hiếm |
| Dữ liệu before | Làm baseline so sánh sau tuning |

## 6. Quy trình review

1. Xác nhận impact nghiệp vụ và mức độ ưu tiên.
2. Thu thập query và metric trước tuning.
3. Phân tích execution plan.
4. Kiểm tra predicate, join, sort, aggregate và parameter.
5. Kiểm tra index hiện có và statistics.
6. Đề xuất một hoặc nhiều phương án: rewrite SQL, thêm index, cập nhật statistics, chia batch, thay đổi schema hoặc cấu hình.
7. Test trên dữ liệu đủ đại diện.
8. So sánh before after bằng metric cụ thể.
9. Ghi khuyến nghị triển khai và rủi ro.

## 7. Tiêu chí before after

| Metric | Cách so sánh |
|---|---|
| Duration | Giảm thời gian chạy trung bình và p95 nếu có |
| CPU | Giảm CPU time hoặc CPU utilization |
| IO | Giảm logical read hoặc physical read |
| Memory | Giảm memory grant hoặc spill |
| Lock | Giảm lock wait, blocking hoặc deadlock |
| Plan stability | Plan ổn định qua nhiều lần chạy |

## 8. Điều kiện không triển khai tuning

1. Không có evidence đủ tin cậy.
2. Kết quả test không cải thiện rõ ràng.
3. Tăng rủi ro write overhead vượt lợi ích đọc.
4. Cải thiện query này nhưng làm xấu workload quan trọng hơn.
5. Không có rollback hoặc cách gỡ thay đổi.

## 9. Tài liệu liên quan

1. STD-015 SQL Coding Standard.
2. STD-016 Indexing Standard.
3. SOP-025 Performance Troubleshooting.

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
