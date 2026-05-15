---
doc_id: STD-016
title: "Database Indexing Standard"
doc_type: standard
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

# STD-016 - Database Indexing Standard

## 1. Mục đích

Chuẩn hóa thiết kế index.

## 2. Mô tả tóm tắt

Quy định nguyên tắc tạo index, composite index, covering index, unique index, index maintenance, kiểm soát index dư thừa và đánh giá tác động write overhead.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thiết kế index

1. Index phải phục vụ truy vấn hoặc ràng buộc cụ thể, không tạo theo cảm tính.
2. Mỗi index mới phải có bằng chứng về truy vấn sử dụng, plan trước và sau, tác động write overhead.
3. Composite index phải đặt thứ tự cột theo selectivity, predicate, join và order pattern thực tế.
4. Index dư thừa phải được phát hiện và loại bỏ có kiểm soát.
5. Index maintenance phải phù hợp DBMS, workload và cửa sổ vận hành.

## 5. Phân loại index

| Loại index | Khi sử dụng | Lưu ý |
|---|---|---|
| Primary key index | Định danh bản ghi | Phải ổn định và không quá rộng |
| Unique index | Bảo vệ uniqueness nghiệp vụ | Cần xác nhận nghiệp vụ trước khi tạo |
| Composite index | Query lọc hoặc join theo nhiều cột | Thứ tự cột rất quan trọng |
| Covering index | Truy vấn đọc nhiều nhưng cần tránh lookup | Cân nhắc kích thước index |
| Filtered hoặc partial index | Chỉ index subset dữ liệu | Cần xác nhận predicate ổn định |
| Full text index | Tìm kiếm text | Không thay thế B Tree cho predicate thông thường |

## 6. Quy trình đề xuất index

1. Thu thập query, tần suất, thời gian chạy và plan hiện tại.
2. Xác định bottleneck chính là scan, lookup, sort, join hoặc cardinality estimate.
3. Đề xuất index và mô tả lý do chọn cột.
4. Kiểm tra index trên môi trường test với dữ liệu đủ đại diện.
5. So sánh trước và sau bằng metric cụ thể.
6. Đánh giá tác động tới insert, update, delete và dung lượng.
7. Tạo RFC nếu triển khai production.

## 7. Điều kiện từ chối tạo index

1. Không có query hoặc workload chứng minh nhu cầu.
2. Query chỉ chạy một lần và không phải workload quan trọng.
3. Index trùng lặp với index hiện có.
4. Index quá rộng, làm tăng write overhead không chấp nhận được.
5. Có giải pháp tốt hơn bằng rewrite query hoặc thay đổi thống kê.

## 8. Checklist review index

| Hạng mục | Câu hỏi kiểm tra |
|---|---|
| Workload | Query có tần suất và impact rõ ràng không |
| Plan | Plan trước và sau có được lưu evidence không |
| Selectivity | Cột đầu tiên có phù hợp predicate không |
| Write impact | Có đánh giá insert, update, delete overhead không |
| Storage | Có dự báo dung lượng index không |
| Maintenance | Có kế hoạch rebuild, reorganize, vacuum hoặc analyze không |

## 9. Tài liệu liên quan

1. STD-015 SQL Coding Standard.
2. SOP-033 Query Tuning Review.
3. SOP-032 Schema Change Review.

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
