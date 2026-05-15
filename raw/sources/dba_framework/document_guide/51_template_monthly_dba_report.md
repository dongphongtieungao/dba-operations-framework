---
status: draft
framework: DBA Operations Framework
document_group: Templates and Metrics
created_for: DBA professional operating system
language: vi
---

# Template Monthly DBA Report

## 1. Thông tin tài liệu

| Trường | Giá trị |
|---|---|
| Mã tài liệu | TPL-DBA-051 |
| Tên tài liệu | Template Monthly DBA Report |
| Nhóm tài liệu | Template and Metrics |
| Mức ưu tiên | 2 |
| Mục đích | Chuẩn hóa báo cáo vận hành tháng |
| Trạng thái | Draft |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Service Owner, Infra Lead, Security Lead khi liên quan |
| Chu kỳ rà soát | Hằng quý hoặc sau mỗi sự cố lớn, thay đổi quy trình, thay đổi công cụ |

## 2. Mục đích

Tài liệu này chuẩn hóa báo cáo vận hành DBA hằng tháng. Báo cáo giúp service owner, manager và DBA team nhìn rõ khối lượng công việc, chất lượng vận hành, rủi ro chính, tình trạng backup, restore, HA, capacity, security, automation và các action item cần theo dõi.

## 3. Đối tượng sử dụng

| Đối tượng | Mục đích sử dụng |
|---|---|
| DBA Manager | Theo dõi hiệu quả vận hành và phân bổ nguồn lực |
| Service Owner | Nắm rủi ro và chất lượng dịch vụ database |
| Infra Lead | Phối hợp capacity, HA, DR, storage, network |
| Security Lead | Theo dõi truy cập, audit, compliance |
| PM hoặc ITSM Manager | Theo dõi ticket, SLA, backlog, change |
| DBA Team | Cải tiến SOP, runbook, automation |

## 4. Thông tin báo cáo

| Trường | Nội dung |
|---|---|
| Report month | YYYY-MM |
| Report owner |  |
| DBA team |  |
| Phạm vi hệ thống |  |
| DBMS trong phạm vi | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Nguồn dữ liệu | ITSM, monitoring, backup system, CMDB, audit log, manual evidence |
| Ngày phát hành |  |
| Người review |  |

## 5. Executive summary

| Nội dung | Tóm tắt |
|---|---|
| Tình trạng vận hành chung | Green, Yellow, Red |
| Điểm tốt trong tháng |  |
| Vấn đề chính |  |
| Rủi ro chính |  |
| Quyết định cần manager hoặc service owner hỗ trợ |  |
| Action item quan trọng |  |

## 6. Ticket summary

| Nhóm ticket | Số lượng | SLA met | SLA missed | Ghi chú |
|---|---:|---:|---:|---|
| Service Request |  |  |  |  |
| Incident |  |  |  |  |
| Change |  |  |  |  |
| Problem |  |  |  |  |
| Access Request |  |  |  |  |
| Performance Support |  |  |  |  |
| Backup, Restore |  |  |  |  |
| HA, DR, Replication |  |  |  |  |

## 7. Incident summary

| Severity | Số lượng | MTTA | MTTR | Lặp lại | PIR required | PIR completed |
|---|---:|---:|---:|---:|---:|---:|
| P1 |  |  |  |  |  |  |
| P2 |  |  |  |  |  |  |
| P3 |  |  |  |  |  |  |
| P4 |  |  |  |  |  |  |
| P5 |  |  |  |  |  |  |

## 8. Change summary

| Chỉ số | Giá trị | Ghi chú |
|---|---:|---|
| Tổng số change |  |  |
| Change thành công |  |  |
| Change rollback |  |  |
| Emergency change |  |  |
| Unauthorized change |  |  |
| Change gây incident |  |  |
| Change success rate |  |  |
| Rollback rate |  |  |

## 9. Backup and restore summary

| Chỉ số | Giá trị | Ghi chú |
|---|---:|---|
| Backup success rate |  |  |
| Backup failed count |  |  |
| Backup overdue count |  |  |
| Restore request count |  |  |
| Restore success rate |  |  |
| Restore drill count |  |  |
| Restore drill pass count |  |  |
| RPO violation count |  |  |
| RTO violation count |  |  |

## 10. HA, DR and replication summary

| Chỉ số | Giá trị | Ghi chú |
|---|---:|---|
| Replication issue count |  |  |
| Replication lag violation count |  |  |
| Planned switchover count |  |  |
| Unplanned failover count |  |  |
| Failover drill count |  |  |
| Failover drill pass count |  |  |
| DR drill count |  |  |
| DR drill pass count |  |  |

## 11. Capacity summary

| Hạng mục | Trạng thái | Top risk | Ghi chú |
|---|---|---|---|
| Database size growth | Green, Yellow, Red |  |  |
| Disk usage | Green, Yellow, Red |  |  |
| Transaction log, WAL, archive log | Green, Yellow, Red |  |  |
| Temp space | Green, Yellow, Red |  |  |
| Connection usage | Green, Yellow, Red |  |  |
| CPU, memory, IO | Green, Yellow, Red |  |  |
| Backup storage | Green, Yellow, Red |  |  |

## 12. Security and compliance summary

| Chỉ số | Giá trị | Ghi chú |
|---|---:|---|
| User created count |  |  |
| User removed count |  |  |
| Privileged access count |  |  |
| Expired access not removed count |  |  |
| Failed login abnormal event count |  |  |
| Audit gap count |  |  |
| Data masking exception count |  |  |
| Permission review completed |  |  |

## 13. Performance and tuning summary

| Hạng mục | Số lượng | Ghi chú |
|---|---:|---|
| Slow query ticket |  |  |
| Blocking incident |  |  |
| Deadlock incident |  |  |
| Query tuning completed |  |  |
| Index recommendation implemented |  |  |
| Statistics or maintenance issue |  |  |
| Resource saturation event |  |  |

## 14. Automation progress

| Hạng mục | Giá trị | Ghi chú |
|---|---:|---|
| SOP có runbook tương ứng |  |  |
| Ticket xử lý bằng automation |  |  |
| Manual production operation count |  |  |
| Automation failure count |  |  |
| New runbook created |  |  |
| Existing runbook improved |  |  |
| Candidate automation backlog |  |  |

## 15. Top risks

| Risk ID | Rủi ro | Mức độ | Owner | Mitigation | Due date | Status |
|---|---|---|---|---|---|---|
| R-001 |  | High, Medium, Low |  |  |  | Open |

## 16. Action item tracking

| ID | Action item | Nguồn | Priority | Owner | Due date | Status |
|---|---|---|---|---|---|---|
| A-001 |  | Incident, PIR, Drill, Audit, Capacity, Automation | P1, P2, P3 |  |  | Open |

## 17. Kết luận và khuyến nghị

| Nội dung | Khuyến nghị |
|---|---|
| Hành động cần làm ngay |  |
| Hành động nên làm trong tháng tới |  |
| Hạng mục cần đầu tư |  |
| Hạng mục cần quyết định từ management |  |
| Hạng mục cần phối hợp app, infra, security |  |

## 99. Lịch sử thay đổi

| Version | Ngày | Người cập nhật | Nội dung thay đổi |
|---|---|---|---|
| 0.1 | YYYY-MM-DD | DBA Team | Khởi tạo bản nháp |
