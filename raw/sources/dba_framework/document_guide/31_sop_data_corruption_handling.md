---
doc_id: SOP-031
title: "SOP Data Corruption Handling"
doc_type: sop
implementation_order: 2
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

# SOP-031 - SOP Data Corruption Handling

## 1. Mục đích

Chuẩn hóa xử lý hỏng dữ liệu.

## 2. Mô tả tóm tắt

Hướng dẫn isolation, xác định phạm vi lỗi, kiểm tra checksum hoặc công cụ tương ứng, quyết định repair hoặc restore, báo cáo rủi ro mất dữ liệu.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thực hiện SOP

1. Không thực hiện thao tác production nếu thiếu ticket hoặc approval bắt buộc.
2. Trước khi thực hiện phải xác định rõ phạm vi ảnh hưởng, rollback path và evidence cần lưu.
3. Với thao tác rủi ro cao, cần peer review hoặc DBA Lead review.
4. Sau khi thực hiện phải có post check và cập nhật ticket.
5. Nếu phát sinh sự cố ngoài dự kiến, phải dừng thao tác, bảo toàn evidence và escalate theo severity.


## 4. Kích hoạt SOP

SOP này được kích hoạt khi có một trong các dấu hiệu sau:

1. Database báo lỗi checksum, page corruption, block corruption hoặc inconsistency.
2. Query trả lỗi đọc dữ liệu bất thường.
3. Backup hoặc restore validation phát hiện dữ liệu không nhất quán.
4. DBMS log ghi nhận lỗi corruption.
5. Ứng dụng ghi nhận mất dữ liệu, sai dữ liệu hoặc lỗi đọc dữ liệu nghiêm trọng.

## 5. Nguyên tắc xử lý

1. Ưu tiên bảo toàn evidence trước khi sửa chữa.
2. Không chạy repair destructive nếu chưa được phê duyệt.
3. Không ghi đè backup hoặc restore point liên quan đến thời điểm nghi ngờ lỗi.
4. Cần đánh giá phạm vi hỏng dữ liệu trước khi quyết định repair hoặc restore.
5. Với production, mọi hành động có nguy cơ mất dữ liệu phải có approval rõ ràng.

## 6. Quy trình thực hiện

### 6.1. Isolation

1. Xác định database, object, file, page, block hoặc tablespace nghi ngờ bị lỗi.
2. Chặn batch job hoặc ứng dụng có thể tiếp tục ghi vào vùng dữ liệu lỗi nếu cần.
3. Thu thập log lỗi, thời điểm phát hiện và thay đổi gần nhất.
4. Bảo vệ backup chain và snapshot liên quan.

### 6.2. Xác định phạm vi lỗi

| DBMS | Công cụ hoặc hướng kiểm tra |
|---|---|
| SQL Server | DBCC CHECKDB, suspect pages, error log |
| PostgreSQL | amcheck, checksum nếu bật, pg logs, logical validation |
| MySQL hoặc MariaDB | CHECK TABLE, InnoDB error log, backup validation |
| Oracle | RMAN validate, DBVERIFY, alert log, block media recovery |
| Azure SQL | DBCC CHECKDB, Azure support diagnostics nếu cần |
| Z DB | Công cụ kiểm tra chính thức của nền tảng và escalation vendor |

### 6.3. Quyết định xử lý

| Tình huống | Hướng xử lý |
|---|---|
| Lỗi nhỏ, có bản sao dữ liệu hợp lệ | Restore object hoặc repair có kiểm soát |
| Lỗi page hoặc block có thể phục hồi | Dùng cơ chế phục hồi page, block hoặc restore phù hợp DBMS |
| Lỗi rộng, ảnh hưởng dữ liệu nghiệp vụ | Restore từ backup hoặc DR theo RPO, RTO |
| Không xác định phạm vi | Escalate DBA Lead, Infra và vendor nếu cần |
| Có nguy cơ mất dữ liệu | Yêu cầu approval từ service owner trước khi hành động |

## 7. Kiểm tra sau xử lý

1. Chạy lại công cụ kiểm tra consistency.
2. Kiểm tra ứng dụng hoặc job nghiệp vụ bị ảnh hưởng.
3. Kiểm tra backup mới sau khi hệ thống ổn định.
4. Kiểm tra replication nếu có.
5. Lập Post Incident Review nếu là sự cố nghiêm trọng.

## 8. Evidence bắt buộc

1. Log lỗi ban đầu.
2. Kết quả kiểm tra consistency.
3. Timeline phát hiện và xử lý.
4. Quyết định repair hoặc restore.
5. Approval nếu có nguy cơ mất dữ liệu.
6. Kết quả validation sau xử lý.

## 9. Tài liệu liên quan

1. POL-010 Backup and Restore Policy.
2. SOP-024 Restore Operation.
3. TPL-047 Post Incident Review.

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
