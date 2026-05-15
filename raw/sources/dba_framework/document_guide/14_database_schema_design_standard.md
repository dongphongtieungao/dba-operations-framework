---
doc_id: STD-014
title: "Database Schema Design Standard"
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

# STD-014 - Database Schema Design Standard

## 1. Mục đích

Chuẩn hóa thiết kế schema.

## 2. Mô tả tóm tắt

Quy định nguyên tắc chọn kiểu dữ liệu, khóa chính, khóa ngoại, chuẩn hóa, denormalization có kiểm soát, partitioning, audit column và soft delete.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thiết kế schema

1. Schema phải phản ánh đúng mô hình nghiệp vụ và boundary của ứng dụng.
2. Dữ liệu quan trọng phải có khóa chính rõ ràng.
3. Quan hệ dữ liệu phải được thể hiện bằng khóa ngoại hoặc ràng buộc tương đương nếu DBMS và workload cho phép.
4. Denormalization chỉ được chấp nhận khi có lý do hiệu năng hoặc báo cáo rõ ràng, có owner và có cơ chế đồng bộ.
5. Mọi thiết kế schema phải xem xét backup, restore, retention, partitioning, audit và data masking.

## 5. Chuẩn chọn kiểu dữ liệu

| Nhóm dữ liệu | Khuyến nghị | Rủi ro cần tránh |
|---|---|---|
| ID nghiệp vụ | Dùng kiểu ổn định, đủ độ dài | Dùng text tự do không kiểm soát |
| ID kỹ thuật | BIGINT, UUID hoặc identity theo yêu cầu hệ thống | Chọn UUID khi không cần phân tán nhưng lại gây overhead index |
| Thời gian | Lưu timezone rõ ràng hoặc chuẩn UTC nếu phù hợp | Lẫn lộn local time và UTC |
| Số tiền | Dùng decimal hoặc numeric | Dùng float cho tiền tệ |
| Trạng thái | Dùng code table hoặc enum có kiểm soát | Dùng string tự do |
| Dữ liệu lớn | Tách storage hoặc dùng LOB có kiểm soát | Đưa payload lớn vào bảng transaction chính |

## 6. Audit column tối thiểu

Các bảng nghiệp vụ quan trọng nên có:

1. `created_at`.
2. `created_by`.
3. `updated_at`.
4. `updated_by`.
5. `deleted_at` nếu dùng soft delete.
6. `deleted_by` nếu cần truy vết xóa mềm.

## 7. Soft delete và retention

1. Chỉ dùng soft delete khi nghiệp vụ cần truy vết hoặc khôi phục logic.
2. Bảng soft delete phải có index phù hợp để không làm chậm truy vấn dữ liệu đang hoạt động.
3. Cần có policy purge hoặc archive để tránh tăng trưởng vô hạn.
4. Báo cáo và query nghiệp vụ phải xử lý nhất quán trạng thái deleted.

## 8. Checklist review schema

| Hạng mục | Câu hỏi kiểm tra |
|---|---|
| Khóa chính | Mỗi bảng quan trọng có primary key không |
| Khóa ngoại | Quan hệ chính có ràng buộc hoặc cơ chế kiểm soát không |
| Kiểu dữ liệu | Có tránh kiểu dữ liệu quá rộng hoặc không phù hợp không |
| Index | Có index cho truy vấn chính và FK quan trọng không |
| Partition | Bảng lớn có chiến lược retention hoặc partition không |
| Audit | Có đủ audit column theo yêu cầu không |
| Masking | Dữ liệu nhạy cảm có thể masking ở non production không |

## 9. Tài liệu liên quan

1. STD-013 Database Naming Convention Standard.
2. STD-016 Database Indexing Standard.
3. STD-017 Database Partitioning Standard.
4. SOP-032 Schema Change Review.

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
