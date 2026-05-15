---
doc_id: STD-017
title: "Database Partitioning Standard"
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

# STD-017 - Database Partitioning Standard

## 1. Mục đích

Chuẩn hóa chiến lược partitioning.

## 2. Mô tả tóm tắt

Mô tả khi nào cần partitioning, chọn partition key, retention theo partition, purge bằng partition, kiểm tra plan và rủi ro vận hành.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Khi nào cần partitioning

Partitioning chỉ nên dùng khi có ít nhất một điều kiện sau:

1. Bảng rất lớn và có xu hướng tăng trưởng liên tục.
2. Có nhu cầu purge hoặc archive theo thời gian.
3. Query thường lọc theo partition key rõ ràng.
4. Maintenance trên toàn bảng gây ảnh hưởng lớn.
5. Cần tách dữ liệu theo tenant, region hoặc lifecycle nếu DBMS và thiết kế cho phép.

## 5. Nguyên tắc chọn partition key

| Tiêu chí | Yêu cầu |
|---|---|
| Tính ổn định | Giá trị partition key ít thay đổi |
| Tính lọc | Query thường xuyên lọc theo key này |
| Tính phân bố | Tránh lệch dữ liệu quá lớn giữa partition |
| Tính vận hành | Hỗ trợ archive, purge, backup hoặc restore theo phần |
| Tính tương thích | Phù hợp giới hạn của DBMS và ORM nếu có |

## 6. Chiến lược partition phổ biến

| Chiến lược | Khi sử dụng | Rủi ro |
|---|---|---|
| Range theo thời gian | Log, transaction, event, audit | Query không có time filter sẽ scan nhiều partition |
| List theo tenant hoặc region | Multi tenant hoặc phân vùng địa lý | Dễ mất cân bằng nếu tenant lớn |
| Hash | Phân tán tải ghi | Khó purge theo thời gian |
| Hybrid | Kết hợp time và tenant | Phức tạp vận hành |

## 7. Quy tắc retention theo partition

1. Retention phải được định nghĩa trước khi tạo partition.
2. Purge bằng drop hoặc detach partition cần có approval và evidence.
3. Phải có kiểm tra dữ liệu trước khi xóa partition.
4. Archive partition phải có checksum hoặc validation tương đương nếu yêu cầu compliance.
5. Không để partition tăng vô hạn mà không có automation tạo partition mới.

## 8. Checklist review partitioning

| Câu hỏi kiểm tra | Kết quả |
|---|---|
| Bảng có đủ lớn để justify partitioning không | Pass hoặc Fail |
| Query chính có dùng partition key không | Pass hoặc Fail |
| Có kế hoạch tạo partition mới tự động không | Pass hoặc Fail |
| Có retention và purge policy không | Pass hoặc Fail |
| Có test plan để xác nhận partition pruning không | Pass hoặc Fail |
| Có rollback plan nếu partitioning gây lỗi ứng dụng không | Pass hoặc Fail |

## 9. Tài liệu liên quan

1. POL-006 Data Lifecycle Management Policy.
2. STD-014 Database Schema Design Standard.
3. STD-020 Capacity Management Standard.

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
