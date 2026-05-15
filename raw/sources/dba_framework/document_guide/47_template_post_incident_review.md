---
status: draft
framework: DBA Operations Framework
document_group: Templates and Metrics
created_for: DBA professional operating system
language: vi
---

# Template Post Incident Review

## 1. Thông tin tài liệu

| Trường | Giá trị |
|---|---|
| Mã tài liệu | TPL-DBA-047 |
| Tên tài liệu | Template Post Incident Review |
| Nhóm tài liệu | Template and Metrics |
| Mức ưu tiên | 2 |
| Mục đích | Chuẩn hóa phân tích sau sự cố |
| Trạng thái | Draft |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Service Owner, Infra Lead, Security Lead khi liên quan |
| Chu kỳ rà soát | Hằng quý hoặc sau mỗi sự cố lớn, thay đổi quy trình, thay đổi công cụ |

## 2. Mục đích

Tài liệu này cung cấp mẫu Post Incident Review cho sự cố database. Mục tiêu là ghi nhận sự thật vận hành, phân tích nguyên nhân gốc rễ, xác định yếu tố góp phần, đưa ra corrective action và preventive action. PIR không dùng để đổ lỗi cá nhân.

## 3. Khi nào cần PIR

| Trường hợp | Cần PIR |
|---|---:|
| Incident P1 hoặc P2 | Có |
| Mất dữ liệu hoặc nghi ngờ mất dữ liệu | Có |
| Failover khẩn cấp | Có |
| Restore production | Có |
| Downtime vượt SLA | Có |
| Sự cố lặp lại từ 2 lần trở lên | Có |
| Sự cố bảo mật liên quan database | Có |
| Near miss nghiêm trọng | Theo quyết định service owner hoặc DBA lead |

## 4. Thông tin sự cố

| Trường | Nội dung cần điền |
|---|---|
| Incident ID |  |
| PIR ID |  |
| Incident title |  |
| Severity | P1, P2, P3, P4, P5 |
| Application, service |  |
| Environment | Production, DR, Staging, UAT, Development |
| DBMS | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Instance, database |  |
| Thời điểm bắt đầu |  |
| Thời điểm phát hiện |  |
| Thời điểm khôi phục |  |
| Thời điểm đóng incident |  |
| Incident commander |  |
| DBA owner |  |
| App owner |  |
| Infra owner |  |
| Security owner nếu liên quan |  |

## 5. Tóm tắt điều hành

| Nội dung | Mô tả |
|---|---|
| Sự cố là gì |  |
| Tác động chính |  |
| Nguyên nhân gốc rễ tóm tắt |  |
| Cách khôi phục |  |
| Rủi ro còn lại |  |
| Bài học chính |  |

## 6. Impact assessment

| Loại tác động | Mô tả | Evidence |
|---|---|---|
| User impact |  |  |
| Business impact |  |  |
| Data impact | Không ảnh hưởng, Có ảnh hưởng, Chưa xác định |  |
| Availability impact |  |  |
| Performance impact |  |  |
| Security impact |  |  |
| SLA impact |  |  |

## 7. Timeline

| Thời gian | Sự kiện | Người hoặc hệ thống ghi nhận | Evidence |
|---|---|---|---|
| YYYY-MM-DD HH:MM TZ | Alert đầu tiên |  |  |
| YYYY-MM-DD HH:MM TZ | Incident được tạo |  |  |
| YYYY-MM-DD HH:MM TZ | DBA bắt đầu điều tra |  |  |
| YYYY-MM-DD HH:MM TZ | Nguyên nhân tạm thời được xác định |  |  |
| YYYY-MM-DD HH:MM TZ | Mitigation được thực hiện |  |  |
| YYYY-MM-DD HH:MM TZ | Dịch vụ phục hồi |  |  |
| YYYY-MM-DD HH:MM TZ | Incident được đóng |  |  |

## 8. Phân tích nguyên nhân

### 8.1. Root cause

| Câu hỏi | Trả lời |
|---|---|
| Nguyên nhân gốc rễ kỹ thuật là gì |  |
| Evidence chứng minh root cause |  |
| Vì sao sự cố không được phát hiện sớm hơn |  |
| Vì sao hệ thống không tự phục hồi hoặc giảm tác động |  |
| Vì sao SOP, alert, automation hiện tại chưa ngăn được sự cố |  |

### 8.2. Contributing factors

| Yếu tố góp phần | Mô tả | Evidence | Có thể phòng ngừa không |
|---|---|---|---|
| Capacity |  |  | Yes, No, Partially |
| Configuration |  |  | Yes, No, Partially |
| Code hoặc query |  |  | Yes, No, Partially |
| Index hoặc statistics |  |  | Yes, No, Partially |
| Backup, restore, replication, HA |  |  | Yes, No, Partially |
| Monitoring hoặc alert |  |  | Yes, No, Partially |
| Change process |  |  | Yes, No, Partially |
| Human operation |  |  | Yes, No, Partially |

## 9. Mitigation và recovery

| Hành động | Loại | Người thực hiện | Thời gian | Kết quả | Evidence |
|---|---|---|---|---|---|
|  | Mitigation, Recovery, Rollback, Failover, Restore |  |  |  |  |

## 10. Corrective action và preventive action

| ID | Action item | Loại | Priority | Owner | Due date | Success criteria | Status |
|---|---|---|---|---|---|---|---|
| CA-001 |  | Corrective | P1, P2, P3 |  |  |  | Open |
| PA-001 |  | Preventive | P1, P2, P3 |  |  |  | Open |

## 11. Cập nhật tài liệu và automation

| Hạng mục | Có cần cập nhật | Nội dung cần cập nhật | Owner | Due date |
|---|---|---|---|---|
| Policy | Yes, No |  |  |  |
| SOP | Yes, No |  |  |  |
| Runbook | Yes, No |  |  |  |
| Alert rule | Yes, No |  |  |  |
| Dashboard | Yes, No |  |  |  |
| Automation script | Yes, No |  |  |  |
| Service catalog | Yes, No |  |  |  |
| CMDB | Yes, No |  |  |  |

## 12. Tiêu chí đóng PIR

1. Timeline đã được xác nhận bởi các bên liên quan chính.
2. Root cause có evidence hỗ trợ, không chỉ là giả định.
3. Action item có owner và deadline rõ ràng.
4. Action item quan trọng được tracking trong ticket hoặc backlog.
5. Tài liệu SOP, runbook, alert hoặc dashboard được cập nhật nếu cần.
6. Service owner chấp nhận rủi ro còn lại nếu chưa xử lý ngay.

## 99. Lịch sử thay đổi

| Version | Ngày | Người cập nhật | Nội dung thay đổi |
|---|---|---|---|
| 0.1 | YYYY-MM-DD | DBA Team | Khởi tạo bản nháp |
