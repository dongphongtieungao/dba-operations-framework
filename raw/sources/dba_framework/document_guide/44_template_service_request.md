---
status: draft
framework: DBA Operations Framework
document_group: Templates and Metrics
doc_id: DBA-TMP-044
language: vi
---

# Template Service Request

## 1. Thông tin tài liệu

| Trường | Giá trị |
|---|---|
| Mã tài liệu | DBA-TMP-044 |
| Tên tài liệu | Template Service Request |
| Nhóm tài liệu | Template and Metrics |
| Mức ưu tiên | 2 |
| Mục đích | Chuẩn hóa yêu cầu dịch vụ DBA |
| Trạng thái | Draft |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Service Owner, Infra Lead, Security Lead khi liên quan |
| Chu kỳ rà soát | Hằng quý hoặc sau mỗi sự cố lớn, thay đổi quy trình, thay đổi công cụ |

## 2. Mục đích

Tài liệu này cung cấp mẫu yêu cầu dịch vụ DBA để bảo đảm mọi yêu cầu được tiếp nhận với đủ thông tin tối thiểu trước khi DBA thực hiện. Mẫu này áp dụng cho các yêu cầu phổ biến như tạo database, tạo user, cấp quyền, restore dữ liệu, backup ad hoc, tuning, export dữ liệu, kiểm tra replication, kiểm tra backup và các tác vụ vận hành khác.

## 3. Phạm vi áp dụng

| Phạm vi | Áp dụng |
|---|---|
| Môi trường | Production, Staging, UAT, Development, DR |
| DBMS | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng liên quan |
| Hệ thống ticket | ServiceNow, Jira, ITSM nội bộ hoặc hệ thống tương đương |
| Loại yêu cầu | Service Request, Fulfillment Request, DBA Support Request |

## 4. Nguyên tắc sử dụng

1. Không thực hiện yêu cầu nếu thiếu requester, business justification, môi trường, database, thời gian mong muốn và approval bắt buộc.
2. Với production, mọi yêu cầu liên quan đến quyền truy cập, restore, export dữ liệu, thay đổi cấu hình hoặc thao tác có rủi ro phải có approval từ owner phù hợp.
3. Với dữ liệu nhạy cảm, phải xác nhận data classification và yêu cầu masking, encryption hoặc audit bổ sung.
4. Mọi thao tác phải để lại evidence theo Template Operation Evidence.

## 5. Mẫu yêu cầu dịch vụ DBA

| Trường | Nội dung cần điền | Bắt buộc | Ghi chú |
|---|---|---:|---|
| Ticket ID | Mã ticket từ ServiceNow, Jira hoặc ITSM | Có | Dùng để trace toàn bộ thao tác |
| Request type | Create DB, Create User, Grant Permission, Restore, Backup Ad hoc, Tuning, Export Data, Other | Có | Chọn một loại chính |
| Requester | Họ tên, email, team | Có | Người gửi yêu cầu |
| Service owner | Họ tên, email, team | Có với production | Người chịu trách nhiệm hệ thống |
| Application name | Tên ứng dụng hoặc service | Có | Map với CMDB nếu có |
| Environment | Production, Staging, UAT, Development, DR | Có | Không dùng tên viết tắt mơ hồ |
| DBMS | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB | Có | Nêu rõ phiên bản nếu biết |
| Instance hoặc Server | Hostname, instance name, endpoint, cluster name | Có | Không ghi password hoặc secret |
| Database name | Tên database liên quan | Có | Nếu tạo mới, ghi tên đề xuất |
| Business justification | Lý do nghiệp vụ | Có | Phải đủ rõ để review |
| Expected result | Kết quả mong muốn | Có | Ví dụ: user có quyền read only trong 7 ngày |
| Required time | Thời gian mong muốn thực hiện | Có | Kèm timezone |
| Impact if not done | Tác động nếu không thực hiện | Có với production | Dùng để ưu tiên |
| Data classification | Public, Internal, Sensitive, Highly Sensitive hoặc theo chuẩn nội bộ | Có nếu liên quan dữ liệu | Dùng để xác định kiểm soát bảo mật |
| Approval | Người phê duyệt, thời gian phê duyệt | Có theo policy | Có thể là link approval trong ITSM |
| Attachment | Script, danh sách user, danh sách table, query, evidence hiện trạng | Tùy loại | Không đính kèm secret |

## 6. Phần thông tin theo từng loại yêu cầu

### 6.1. Tạo database

| Trường | Nội dung cần điền |
|---|---|
| Database name đề xuất |  |
| Owner ứng dụng |  |
| Size ban đầu |  |
| Growth dự kiến |  |
| RPO, RTO yêu cầu |  |
| HA, DR yêu cầu |  |
| Backup policy mong muốn |  |
| Charset, collation hoặc locale |  |
| Network access requirement |  |
| Monitoring requirement |  |

### 6.2. Tạo user hoặc cấp quyền

| Trường | Nội dung cần điền |
|---|---|
| User type | Human user, service account, application account |
| User identifier |  |
| Role cần cấp |  |
| Database, schema, object scope |  |
| Thời hạn quyền |  |
| Lý do cần quyền |  |
| Approval của data owner |  |
| Yêu cầu MFA hoặc privileged access |  |

### 6.3. Restore dữ liệu

| Trường | Nội dung cần điền |
|---|---|
| Source database |  |
| Target database |  |
| Target environment |  |
| Recovery point yêu cầu |  |
| Restore type | Full, PITR, transaction log, WAL, clone, other |
| Có ghi đè dữ liệu hiện tại không | Yes, No |
| Validation owner |  |
| Dữ liệu nhạy cảm cần masking |  |
| Thời gian chấp nhận downtime |  |

### 6.4. Backup ad hoc

| Trường | Nội dung cần điền |
|---|---|
| Database cần backup |  |
| Lý do backup ad hoc |  |
| Loại backup | Full, differential, log, snapshot, export |
| Vị trí lưu trữ mong muốn |  |
| Thời gian giữ backup |  |
| Encryption requirement |  |

### 6.5. Query tuning hoặc performance support

| Trường | Nội dung cần điền |
|---|---|
| Triệu chứng | Slow query, timeout, high CPU, blocking, deadlock, high IO |
| Query hoặc module liên quan |  |
| Thời điểm phát sinh |  |
| Tần suất |  |
| Impact nghiệp vụ |  |
| Evidence hiện có | Log, APM, execution plan, screenshot dashboard |
| Thời gian cần hỗ trợ |  |

### 6.6. Export dữ liệu

| Trường | Nội dung cần điền |
|---|---|
| Database, schema, table |  |
| Điều kiện lọc dữ liệu |  |
| Mục đích sử dụng |  |
| Người nhận dữ liệu |  |
| Data classification |  |
| Masking requirement |  |
| Encryption requirement |  |
| Approval của data owner |  |
| Thời hạn lưu file export |  |

## 7. Checklist tiếp nhận của DBA

| # | Nội dung kiểm tra | Kết quả | Ghi chú |
|---:|---|---|---|
| 1 | Ticket có đủ thông tin bắt buộc | Pass, Fail, N/A |  |
| 2 | Request type đã được phân loại đúng | Pass, Fail, N/A |  |
| 3 | Môi trường production đã có approval phù hợp | Pass, Fail, N/A |  |
| 4 | Không có secret trong ticket hoặc attachment | Pass, Fail, N/A |  |
| 5 | Đã xác định SOP hoặc runbook tương ứng | Pass, Fail, N/A |  |
| 6 | Đã xác định risk level | Pass, Fail, N/A |  |
| 7 | Đã xác định evidence cần thu thập | Pass, Fail, N/A |  |

## 8. Mapping SOP và template liên quan

| Loại yêu cầu | SOP chính | Template hoặc evidence liên quan |
|---|---|---|
| Tạo user, cấp quyền | SOP User and Permission Management | Template Operation Evidence |
| Backup ad hoc | SOP Backup Operation | Template Operation Evidence |
| Restore | SOP Restore Operation | Template Restore Drill Report nếu là drill |
| Performance support | SOP Performance Troubleshooting | Template Operation Evidence, Template PIR nếu có incident |
| Blocking hoặc deadlock | SOP Blocking and Deadlock Handling | Template Operation Evidence, Template PIR nếu có incident |
| Failover hoặc switchover | SOP HA Failover and Switchover | Template Failover Drill Report, Template Emergency Change |
| DR drill | SOP Disaster Recovery Drill | Template Restore Drill Report, Template Failover Drill Report |

## 9. Tiêu chí hoàn tất yêu cầu

1. Kết quả thực hiện khớp với expected result trong ticket.
2. Có evidence vận hành đầy đủ.
3. Requester hoặc service owner xác nhận nếu yêu cầu cần nghiệm thu.
4. Ticket được cập nhật kết quả, thời gian thực hiện, rủi ro còn lại nếu có.
5. Nếu phát hiện lỗi quy trình, phải tạo action item để cập nhật SOP, runbook hoặc template liên quan.

## 99. Lịch sử thay đổi

| Version | Ngày | Người cập nhật | Nội dung thay đổi |
|---|---|---|---|
| 0.1 | 2026-05-18 | DBA Team | Khởi tạo bản nháp |
| 0.2 | 2026-05-18 | DBA Team | Chuẩn hóa doc ID |
