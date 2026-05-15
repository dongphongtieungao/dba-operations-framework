---
status: draft
framework: DBA Operations Framework
document_group: Templates and Metrics
created_for: DBA professional operating system
language: vi
---

# Template Restore Drill Report

## 1. Thông tin tài liệu

| Trường | Giá trị |
|---|---|
| Mã tài liệu | TPL-DBA-049 |
| Tên tài liệu | Template Restore Drill Report |
| Nhóm tài liệu | Template and Metrics |
| Mức ưu tiên | 1 |
| Mục đích | Chuẩn hóa báo cáo diễn tập restore |
| Trạng thái | Draft |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Service Owner, Infra Lead, Security Lead khi liên quan |
| Chu kỳ rà soát | Hằng quý hoặc sau mỗi sự cố lớn, thay đổi quy trình, thay đổi công cụ |

## 2. Mục đích

Tài liệu này chuẩn hóa báo cáo diễn tập restore để kiểm chứng khả năng phục hồi dữ liệu, đo RTO, đo RPO, xác định lỗi quy trình và tạo action item cải tiến. Restore drill là bằng chứng quan trọng cho Backup and Restore Policy.

## 3. Phạm vi restore drill

| Loại drill | Mô tả |
|---|---|
| Restore validation định kỳ | Khôi phục backup sang môi trường kiểm thử để kiểm tra backup có dùng được không |
| Point in time recovery drill | Khôi phục về một thời điểm cụ thể theo yêu cầu RPO |
| Full database restore drill | Khôi phục toàn bộ database |
| Partial restore drill | Khôi phục schema, table, tablespace hoặc filegroup nếu DBMS hỗ trợ |
| Cross environment restore | Restore từ production sang non production có kiểm soát masking nếu cần |
| DR restore drill | Restore tại site hoặc region DR |

## 4. Thông tin chung

| Trường | Nội dung |
|---|---|
| Drill ID |  |
| Related ticket hoặc change |  |
| Drill date |  |
| Người thực hiện |  |
| Người quan sát hoặc reviewer |  |
| Service owner |  |
| Application, service |  |
| Environment nguồn |  |
| Environment đích |  |
| DBMS, version |  |
| Source instance, database |  |
| Target instance, database |  |
| Data classification |  |
| SOP sử dụng | SOP Restore Operation |
| Runbook sử dụng nếu có |  |

## 5. Mục tiêu drill

| Mục tiêu | Giá trị mục tiêu | Kết quả thực tế | Đạt, không đạt |
|---|---|---|---|
| Xác minh backup có thể restore |  |  |  |
| Đo RTO thực tế |  |  |  |
| Đo RPO thực tế |  |  |  |
| Kiểm tra validation sau restore |  |  |  |
| Kiểm tra quy trình approval và evidence |  |  |  |
| Kiểm tra masking nếu restore sang non production |  |  |  |

## 6. Thông tin backup source

| Trường | Nội dung |
|---|---|
| Backup type | Full, differential, incremental, transaction log, WAL, snapshot, RMAN, other |
| Backup location |  |
| Backup timestamp |  |
| Recovery point requested |  |
| Recovery point achieved |  |
| Backup size |  |
| Encryption status |  |
| Backup integrity check result |  |
| Backup chain completeness |  |

## 7. Timeline restore

| Thời gian | Sự kiện | Người thực hiện | Evidence |
|---|---|---|---|
| YYYY-MM-DD HH:MM TZ | Bắt đầu chuẩn bị |  |  |
| YYYY-MM-DD HH:MM TZ | Xác nhận backup source |  |  |
| YYYY-MM-DD HH:MM TZ | Bắt đầu restore |  |  |
| YYYY-MM-DD HH:MM TZ | Restore hoàn tất |  |  |
| YYYY-MM-DD HH:MM TZ | Bắt đầu validation |  |  |
| YYYY-MM-DD HH:MM TZ | Validation hoàn tất |  |  |
| YYYY-MM-DD HH:MM TZ | Drill hoàn tất |  |  |

## 8. Kết quả restore

| Trường | Nội dung |
|---|---|
| Restore status | Success, Partial Success, Failed |
| Restore duration |  |
| RTO target |  |
| RTO actual |  |
| RPO target |  |
| RPO actual |  |
| Sai lệch so với mục tiêu |  |
| Nguyên nhân sai lệch nếu có |  |
| Có cần rerun drill không | Yes, No |

## 9. Validation sau restore

| # | Kiểm tra | Kết quả mong muốn | Kết quả thực tế | Evidence |
|---:|---|---|---|---|
| 1 | Database online | Online |  |  |
| 2 | Object count | Khớp baseline hoặc chênh lệch được giải thích |  |  |
| 3 | Row count sample | Khớp baseline hoặc chênh lệch được giải thích |  |  |
| 4 | Checksum hoặc consistency check | Pass nếu DBMS hỗ trợ |  |  |
| 5 | User, role, permission | Phù hợp môi trường đích |  |  |
| 6 | Application smoke test | Pass nếu có app test |  |  |
| 7 | Masking validation | Không còn dữ liệu thật nếu yêu cầu masking |  |  |
| 8 | Error log | Không có lỗi nghiêm trọng |  |  |

## 10. Lỗi phát sinh và xử lý

| Lỗi | Thời điểm | Tác động | Cách xử lý | Người xử lý | Trạng thái |
|---|---|---|---|---|---|
|  |  |  |  |  | Open, Closed |

## 11. Kết luận drill

| Nội dung | Kết quả |
|---|---|
| Restore drill đạt yêu cầu không | Yes, No, Partially |
| Backup có thể sử dụng để phục hồi không | Yes, No, Unknown |
| RTO đạt mục tiêu không | Yes, No, N/A |
| RPO đạt mục tiêu không | Yes, No, N/A |
| Quy trình restore có đủ rõ không | Yes, No, Partially |
| Evidence có đủ audit không | Yes, No, Partially |

## 12. Action item cải tiến

| ID | Action item | Priority | Owner | Due date | Success criteria | Status |
|---|---|---|---|---|---|---|
| RD-001 |  | P1, P2, P3 |  |  |  | Open |

## 13. Tài liệu cần cập nhật

| Tài liệu | Có cần cập nhật | Nội dung cập nhật |
|---|---|---|
| Backup and Restore Policy | Yes, No |  |
| SOP Backup Operation | Yes, No |  |
| SOP Restore Operation | Yes, No |  |
| Runbook Backup Verification | Yes, No |  |
| Runbook Restore Validation | Yes, No |  |
| Template Operation Evidence | Yes, No |  |

## 99. Lịch sử thay đổi

| Version | Ngày | Người cập nhật | Nội dung thay đổi |
|---|---|---|---|
| 0.1 | YYYY-MM-DD | DBA Team | Khởi tạo bản nháp |
