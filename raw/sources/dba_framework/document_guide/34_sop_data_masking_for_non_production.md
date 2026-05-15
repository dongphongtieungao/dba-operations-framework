---
doc_id: SOP-034
title: "SOP Data Masking for Non Production"
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

# SOP-034 - SOP Data Masking for Non Production

## 1. Mục đích

Đảm bảo môi trường test không dùng dữ liệu thật.

## 2. Mô tả tóm tắt

Hướng dẫn phân loại dữ liệu nhạy cảm, masking rule, kiểm tra dữ liệu sau masking, phê duyệt và evidence trước khi bàn giao môi trường.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thực hiện SOP

1. Không thực hiện thao tác production nếu thiếu ticket hoặc approval bắt buộc.
2. Trước khi thực hiện phải xác định rõ phạm vi ảnh hưởng, rollback path và evidence cần lưu.
3. Với thao tác rủi ro cao, cần peer review hoặc DBA Lead review.
4. Sau khi thực hiện phải có post check và cập nhật ticket.
5. Nếu phát sinh sự cố ngoài dự kiến, phải dừng thao tác, bảo toàn evidence và escalate theo severity.


## 4. Nguyên tắc masking

1. Non production không được dùng dữ liệu thật nếu có dữ liệu nhạy cảm và không có phê duyệt đặc biệt.
2. Masking phải giữ được tính hợp lệ nghiệp vụ đủ để test.
3. Dữ liệu sau masking không được có khả năng suy ngược về dữ liệu thật.
4. Quy tắc masking phải được versioning và review.
5. Kết quả masking phải có evidence trước khi bàn giao môi trường.

## 5. Phân loại dữ liệu cần masking

| Nhóm dữ liệu | Ví dụ | Hướng masking |
|---|---|---|
| Thông tin định danh cá nhân | tên, số giấy tờ, số điện thoại, email | thay thế bằng dữ liệu giả hợp lệ |
| Dữ liệu tài chính | tài khoản, thẻ, số dư | tokenization, redaction hoặc random có kiểm soát |
| Dữ liệu bảo mật | secret, token, key | xóa hoặc thay bằng placeholder |
| Dữ liệu khách hàng | địa chỉ, hợp đồng, mã khách hàng | pseudonymization |
| Log nghiệp vụ nhạy cảm | payload, message, request body | redaction theo field |

## 6. Quy trình masking

1. Xác định nguồn dữ liệu và mục tiêu non production.
2. Phân loại dữ liệu nhạy cảm theo policy.
3. Xác định rule masking cho từng field.
4. Chạy masking trên bản sao dữ liệu, không chạy trực tiếp trên production.
5. Kiểm tra referential integrity sau masking.
6. Kiểm tra dữ liệu mẫu để xác nhận không còn dữ liệu thật.
7. Lưu evidence và bàn giao môi trường.

## 7. Kiểm tra sau masking

| Kiểm tra | Mục tiêu |
|---|---|
| Sensitive field sampling | Xác nhận field nhạy cảm đã bị masking |
| Referential integrity | Đảm bảo ứng dụng test không lỗi quan hệ dữ liệu |
| Application smoke test | Xác nhận dữ liệu test còn dùng được |
| Duplicate rule check | Đảm bảo mapping nhất quán nếu cần |
| Audit evidence | Lưu kết quả kiểm tra vào ticket |

## 8. Điều kiện bàn giao môi trường

1. Có ticket yêu cầu dữ liệu non production.
2. Có phê duyệt owner dữ liệu nếu cần.
3. Có danh sách rule masking đã áp dụng.
4. Có evidence kiểm tra sau masking.
5. Có xác nhận từ requester hoặc test lead.

## 9. Tài liệu liên quan

1. POL-006 Data Lifecycle Management Policy.
2. POL-007 Database Security Policy.
3. POL-011 Audit and Compliance Policy.

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
