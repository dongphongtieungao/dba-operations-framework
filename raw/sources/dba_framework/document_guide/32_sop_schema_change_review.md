---
doc_id: SOP-032
title: "SOP Schema Change Review"
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

# SOP-032 - SOP Schema Change Review

## 1. Mục đích

Chuẩn hóa review thay đổi schema.

## 2. Mô tả tóm tắt

Hướng dẫn review DDL, impact index, locking risk, data migration risk, rollback script, downtime, compatibility và phê duyệt trước khi triển khai.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thực hiện SOP

1. Không thực hiện thao tác production nếu thiếu ticket hoặc approval bắt buộc.
2. Trước khi thực hiện phải xác định rõ phạm vi ảnh hưởng, rollback path và evidence cần lưu.
3. Với thao tác rủi ro cao, cần peer review hoặc DBA Lead review.
4. Sau khi thực hiện phải có post check và cập nhật ticket.
5. Nếu phát sinh sự cố ngoài dự kiến, phải dừng thao tác, bảo toàn evidence và escalate theo severity.


## 4. Khi nào cần review schema change

1. Tạo, sửa hoặc xóa table, column, index, constraint, partition.
2. Thay đổi kiểu dữ liệu hoặc độ dài dữ liệu.
3. Thay đổi khóa chính, khóa ngoại hoặc unique constraint.
4. Migration dữ liệu khối lượng lớn.
5. Thay đổi có thể gây lock, downtime hoặc plan regression.

## 5. Input bắt buộc

| Input | Mô tả |
|---|---|
| RFC hoặc ticket | Mã yêu cầu thay đổi |
| DDL script | Script triển khai |
| Rollback script | Script hoàn tác hoặc phương án restore |
| Impact analysis | Phân tích ảnh hưởng tới app, job, report, replication |
| Test evidence | Kết quả chạy ở môi trường test |
| Maintenance window | Khung giờ triển khai nếu cần |

## 6. Quy trình review

1. Kiểm tra mục đích nghiệp vụ của thay đổi.
2. Kiểm tra naming convention.
3. Kiểm tra tương thích kiểu dữ liệu và constraint.
4. Kiểm tra tác động lock và thời gian chạy dự kiến.
5. Kiểm tra index, statistics và execution plan liên quan.
6. Kiểm tra data migration script nếu có.
7. Kiểm tra rollback script hoặc recovery plan.
8. Kiểm tra monitoring và alert sau triển khai.
9. Ghi kết luận review: approved, approved with condition hoặc rejected.

## 7. Rủi ro cần đánh giá

| Rủi ro | Câu hỏi kiểm tra |
|---|---|
| Locking | DDL có lock bảng lớn không |
| Downtime | Có cần dừng ứng dụng không |
| Data loss | Có xóa hoặc truncate dữ liệu không |
| Compatibility | Ứng dụng version cũ có còn chạy được không |
| Replication | DDL có ảnh hưởng replica không |
| Rollback | Có thể rollback trong thời gian cho phép không |

## 8. Kết quả review

| Kết quả | Điều kiện |
|---|---|
| Approved | Đủ script, rollback, test evidence và rủi ro chấp nhận được |
| Approved with condition | Cần bổ sung điều kiện triển khai hoặc monitoring |
| Rejected | Thiếu evidence, rủi ro quá cao hoặc không có rollback hợp lệ |

## 9. Tài liệu liên quan

1. POL-009 Change Management Policy.
2. STD-014 Schema Design Standard.
3. TPL-045 Request for Change.

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
