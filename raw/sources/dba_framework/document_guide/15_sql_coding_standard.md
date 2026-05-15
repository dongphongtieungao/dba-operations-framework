---
doc_id: STD-015
title: "SQL Coding Standard"
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

# STD-015 - SQL Coding Standard

## 1. Mục đích

Chuẩn hóa cách viết SQL.

## 2. Mô tả tóm tắt

Quy định format SQL, alias, join, transaction, error handling, tránh SELECT sao, tránh function trên indexed column, tránh implicit conversion và các anti pattern phổ biến.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc viết SQL

1. SQL phải rõ nghĩa, dễ review và có thể giải thích được plan thực thi.
2. Không dùng `SELECT *` trong code production, job vận hành hoặc report chính thức.
3. Không dùng function trực tiếp trên indexed column trong điều kiện lọc nếu làm mất khả năng dùng index.
4. Không phụ thuộc implicit conversion giữa kiểu dữ liệu khác nhau.
5. Transaction phải ngắn, có mục tiêu rõ ràng và có xử lý lỗi.

## 5. Format SQL chuẩn

| Thành phần | Quy định |
|---|---|
| SELECT | Liệt kê rõ column cần lấy |
| JOIN | Ghi rõ loại join và điều kiện join |
| WHERE | Điều kiện lọc phải dùng kiểu dữ liệu phù hợp |
| ORDER BY | Chỉ dùng khi cần thứ tự kết quả |
| Alias | Dùng alias ngắn, nhất quán, không gây mơ hồ |
| Comment | Comment cho logic nghiệp vụ phức tạp, không comment thừa |

## 6. Anti pattern cần tránh

| Anti pattern | Tác động |
|---|---|
| SELECT sao | Tăng IO, tăng memory, dễ lỗi khi schema thay đổi |
| Function trên indexed column | Có thể làm optimizer không dùng index |
| Implicit conversion | Có thể scan bảng hoặc sai lệch plan |
| Transaction dài | Gây blocking, deadlock, log growth |
| Query không giới hạn trên bảng lớn | Tăng tải CPU, IO, memory |
| Dynamic SQL không kiểm soát | Rủi ro SQL injection và khó audit |
| Cursor không cần thiết | Tăng thời gian xử lý và resource |

## 7. Quy tắc transaction

1. Chỉ mở transaction khi cần đảm bảo tính nhất quán.
2. Không gọi API hoặc chờ input người dùng trong transaction.
3. Với batch lớn, cần chia nhỏ theo chunk có kiểm soát.
4. Phải có rollback path rõ ràng khi lỗi.
5. Cần kiểm tra lock impact trước khi chạy production.

## 8. Checklist review SQL

| Câu hỏi kiểm tra | Kết quả |
|---|---|
| SQL có liệt kê cột cụ thể không | Pass hoặc Fail |
| Điều kiện lọc có dùng index được không | Pass hoặc Fail |
| Join có đúng cardinality không | Pass hoặc Fail |
| Plan có scan bảng lớn ngoài dự kiến không | Pass hoặc Fail |
| Transaction có nguy cơ blocking không | Pass hoặc Fail |
| Có rollback hoặc retry strategy không | Pass hoặc Fail |

## 9. Tài liệu liên quan

1. STD-016 Database Indexing Standard.
2. SOP-033 Query Tuning Review.
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
