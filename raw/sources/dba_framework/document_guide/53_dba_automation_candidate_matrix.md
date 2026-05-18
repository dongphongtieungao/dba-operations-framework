---
doc_id: DBA-AUT-053
title: "DBA Automation Candidate Matrix"
doc_type: management
priority: 2
status: draft
owner: DBA Team
reviewer: DBA Lead
approver: Service Owner
version: 0.2
created_date: 2026-05-15
last_updated: 2026-05-18
language: vi
framework: DBA Operations Framework
related_documents:
  - DBA-RBK-038 Runbook Backup Verification
  - DBA-RBK-039 Runbook Restore Validation
  - DBA-RBK-041 Runbook Failover Execution
  - DBA-RBK-043 Runbook Capacity Report
  - DBA-MET-052 DBA KPI and Metrics Catalog
---

# DBA-AUT-053 - DBA Automation Candidate Matrix

## 1. Mục đích

Ưu tiên tự động hóa tác vụ DBA.

## 2. Mô tả tóm tắt

Bảng đánh giá task theo tần suất, rủi ro, effort thủ công, khả năng chuẩn hóa và thứ tự ưu tiên tự động hóa.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Tiêu chí đánh giá tự động hóa

| Tiêu chí | Ý nghĩa | Thang điểm gợi ý |
|---|---|---|
| Frequency | Tần suất task xuất hiện | 1 đến 5 |
| Risk | Rủi ro khi làm thủ công | 1 đến 5 |
| Manual effort | Thời gian thao tác thủ công | 1 đến 5 |
| Standardizable | Khả năng chuẩn hóa input và output | 1 đến 5 |
| Evidence need | Nhu cầu audit evidence | 1 đến 5 |
| Business impact | Tác động tới dịch vụ | 1 đến 5 |

## 5. Công thức ưu tiên

Điểm ưu tiên có thể tính như sau:

```text
Automation Priority Score = Frequency + Risk + Manual Effort + Standardizable + Evidence Need + Business Impact
```

Phân loại:

| Điểm | Mức ưu tiên | Ý nghĩa |
|---|---|---|
| 24 đến 30 | High | Nên tự động hóa sớm |
| 16 đến 23 | Medium | Nên tự động hóa sau nhóm high |
| 10 đến 15 | Low | Chỉ tự động hóa nếu có nhu cầu rõ |
| Dưới 10 | Defer | Chưa nên tự động hóa |

## 6. Candidate matrix ban đầu

| Task | Frequency | Risk | Manual effort | Standardizable | Priority |
|---|---:|---:|---:|---:|---|
| Backup verification | 5 | 5 | 2 | 5 | High |
| Restore validation | 3 | 5 | 3 | 4 | High |
| Database health check | 5 | 4 | 3 | 5 | High |
| User provisioning | 4 | 4 | 2 | 5 | High |
| Database provisioning | 3 | 4 | 4 | 4 | High |
| Capacity report | 4 | 3 | 3 | 5 | High |
| Add replica | 2 | 5 | 4 | 3 | Medium |
| Failover execution | 1 | 5 | 5 | 3 | Medium |
| Query tuning | 3 | 3 | 5 | 2 | Medium |
| Data corruption handling | 1 | 5 | 5 | 1 | Low for automation, High for SOP |

## 7. Điều kiện đưa vào backlog automation

1. Có SOP ổn định.
2. Có input và output rõ ràng.
3. Có test case hoặc validation rule.
4. Có rollback hoặc safe failure behavior.
5. Có owner vận hành script sau khi triển khai.

## 8. Lifecycle của automation

1. Candidate.
2. Approved.
3. Designed.
4. Implemented.
5. Tested.
6. Production ready.
7. Active.
8. Deprecated.
9. Retired.

## 9. Tài liệu liên quan

1. RBK-038 Backup Verification.
2. RBK-039 Restore Validation.
3. RBK-042 Database Health Check.

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
