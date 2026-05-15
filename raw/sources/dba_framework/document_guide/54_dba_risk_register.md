---
doc_id: RSK-054
title: "DBA Risk Register"
doc_type: management
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

# RSK-054 - DBA Risk Register

## 1. Mục đích

Theo dõi rủi ro vận hành database.

## 2. Mô tả tóm tắt

Danh sách rủi ro như backup chưa kiểm chứng, thiếu DR drill, quyền production quá rộng, replication lag, capacity risk, kèm owner và mitigation.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Cấu trúc risk register

| Trường | Mô tả |
|---|---|
| risk_id | Mã rủi ro |
| risk_title | Tên rủi ro |
| category | Backup, security, HA, capacity, performance, compliance |
| description | Mô tả rủi ro |
| impact | Tác động nếu xảy ra |
| likelihood | Khả năng xảy ra |
| severity | Mức độ tổng hợp |
| owner | Người chịu trách nhiệm |
| mitigation | Biện pháp giảm thiểu |
| due_date | Hạn xử lý |
| status | Open, In progress, Mitigated, Accepted, Closed |

## 5. Danh sách rủi ro mẫu

| Risk ID | Rủi ro | Category | Severity | Mitigation |
|---|---|---|---|---|
| RSK-001 | Backup chưa được restore drill định kỳ | Backup | High | Thiết lập restore drill hàng tháng hoặc hàng quý |
| RSK-002 | Thiếu DR drill cho hệ thống critical | HA DR | High | Lập lịch DR drill và đo RTO, RPO |
| RSK-003 | Quyền production quá rộng | Security | High | Access review và least privilege |
| RSK-004 | Replication lag thường xuyên vượt ngưỡng | HA | Medium | Tuning replication, network, workload |
| RSK-005 | Disk hoặc log gần đầy | Capacity | High | Forecast, mở rộng hoặc purge |
| RSK-006 | Không có owner rõ ràng cho database cũ | Governance | Medium | Cập nhật service catalog và CMDB |
| RSK-007 | Alert quá nhiều false positive | Monitoring | Medium | Review threshold và alert rule |

## 6. Đánh giá severity

| Impact | Likelihood thấp | Likelihood trung bình | Likelihood cao |
|---|---|---|---|
| Low | Low | Low | Medium |
| Medium | Low | Medium | High |
| High | Medium | High | Critical |
| Critical | High | Critical | Critical |

## 7. Quy trình quản lý risk

1. Ghi nhận rủi ro từ incident, audit, monitoring, review hoặc drill.
2. Gán owner rõ ràng.
3. Đánh giá impact, likelihood và severity.
4. Xác định mitigation hoặc quyết định accept risk.
5. Theo dõi deadline và trạng thái.
6. Review risk register trong monthly DBA report.
7. Đóng rủi ro khi có evidence xử lý.

## 8. Điều kiện escalate

1. Rủi ro critical quá hạn.
2. Rủi ro high không có owner.
3. Rủi ro liên quan mất dữ liệu hoặc vi phạm compliance.
4. Rủi ro lặp lại sau khi đã mitigation.

## 9. Tài liệu liên quan

1. TPL-047 Post Incident Review.
2. TPL-051 Monthly DBA Report.
3. MET-052 KPI and Metrics Catalog.

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
