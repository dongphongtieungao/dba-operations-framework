---
doc_id: STD-013
title: "Database Naming Convention Standard"
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

# STD-013 - Database Naming Convention Standard

## 1. Mục đích

Chuẩn hóa quy ước đặt tên.

## 2. Mô tả tóm tắt

Quy định cách đặt tên instance, database, schema, table, column, index, constraint, role, user, job, backup file và monitoring object.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc đặt tên

1. Tên phải phản ánh đúng mục đích sử dụng, môi trường và phạm vi sở hữu.
2. Không dùng tên mơ hồ như `test`, `newdb`, `temp1`, `data2` cho tài nguyên có vòng đời dài.
3. Không đưa thông tin nhạy cảm vào tên tài nguyên, ví dụ tên khách hàng cá nhân, mã định danh cá nhân hoặc nội dung nghiệp vụ bí mật.
4. Tên phải ổn định để không gây lỗi dependency, monitoring, backup hoặc automation.
5. Quy ước tên phải được áp dụng thống nhất từ database đến monitoring object.

## 5. Quy ước tên khuyến nghị

| Đối tượng | Pattern khuyến nghị | Ví dụ | Ghi chú |
|---|---|---|---|
| Instance | `<env>_<service>_<dbms>_<seq>` | `prd_order_sql_01` | Dùng cho inventory và monitoring |
| Database | `<domain>_<service>_<purpose>` | `sales_order_core` | Tránh tên quá dài hoặc quá chung |
| Schema | `<domain>` hoặc `<bounded_context>` | `billing` | Không dùng schema cá nhân trong production |
| Table | `<entity>` hoặc `<entity_plural>` | `customer_account` | Chọn một convention và áp dụng nhất quán |
| Column | `<business_attribute>` | `created_at` | Tránh viết tắt khó hiểu |
| Primary key | `pk_<table>` | `pk_customer_account` | Có thể theo giới hạn độ dài từng DBMS |
| Foreign key | `fk_<child>_<parent>` | `fk_order_customer` | Nên thể hiện quan hệ chính |
| Index | `idx_<table>_<column_group>` | `idx_order_customer_created_at` | Với unique dùng prefix `ux` nếu cần |
| Role | `role_<scope>_<permission>` | `role_order_readonly` | Tách role nghiệp vụ và role kỹ thuật |
| User | `<system>_<purpose>_<env>` | `svc_order_app_prd` | Service account phải phân biệt rõ môi trường |
| Job | `job_<db>_<purpose>` | `job_order_backup_check` | Dễ mapping với alert |
| Backup file | `<db>_<type>_<timestamp>` | `order_full_20260515_010000` | Cần đủ thông tin phục hồi |
| Monitoring object | `<env>.<dbms>.<instance>.<metric>` | `prd.sql.order01.backup_age` | Hỗ trợ correlation |

## 6. Quy tắc cấm

1. Không dùng tên chứa ký tự đặc biệt không cần thiết.
2. Không dùng khoảng trắng trong tên object database.
3. Không dùng tên phụ thuộc vào tên cá nhân của DBA hoặc developer.
4. Không tái sử dụng tên đã từng dùng cho database đã decommission nếu có nguy cơ nhầm lẫn evidence.
5. Không đổi tên object production nếu chưa có impact analysis và rollback plan.

## 7. Checklist review

| Câu hỏi kiểm tra | Kết quả |
|---|---|
| Tên có thể hiện môi trường hoặc phạm vi sử dụng không | Pass hoặc Fail |
| Tên có tránh thông tin nhạy cảm không | Pass hoặc Fail |
| Tên có phù hợp giới hạn độ dài của DBMS không | Pass hoặc Fail |
| Tên có tương thích với backup, monitoring và automation không | Pass hoặc Fail |
| Tên có được ghi vào inventory hoặc CMDB không | Pass hoặc Fail |

## 9. Tài liệu liên quan

1. DOC-005 DBA Document Governance Standard.
2. STD-014 Database Schema Design Standard.
3. RBK-036 Runbook Database Provisioning.

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
