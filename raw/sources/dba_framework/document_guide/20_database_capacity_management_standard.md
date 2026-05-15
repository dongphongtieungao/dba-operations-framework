---
doc_id: STD-020
title: "Database Capacity Management Standard"
doc_type: standard
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

# STD-020 - Database Capacity Management Standard

## 1. Mục đích

Quản lý dung lượng và tăng trưởng.

## 2. Mô tả tóm tắt

Quy định theo dõi disk, IOPS, CPU, memory, connection, transaction log, WAL, temp space, growth forecast và ngưỡng mở rộng tài nguyên.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Thành phần capacity cần quản lý

| Thành phần | Nội dung theo dõi |
|---|---|
| Disk data | Data file size, tablespace size, database size, growth rate |
| Transaction log hoặc WAL | Log size, generation rate, reuse, archive lag |
| Temp space | Tempdb, temp tablespace, temporary file, spill usage |
| CPU | Average, peak, saturation, noisy workload |
| Memory | Buffer usage, cache hit, memory pressure |
| IOPS | Read IOPS, write IOPS, latency, queue depth |
| Connection | Active, idle, max usage, connection storm |
| Backup storage | Backup size, retention usage, growth trend |

## 5. Ngưỡng capacity khuyến nghị

| Mức | Điều kiện | Hành động |
|---|---|---|
| Normal | Dư tài nguyên theo baseline | Theo dõi định kỳ |
| Watch | Dự báo vượt 70 phần trăm trong 30 ngày | Tạo ticket review |
| Warning | Dự báo vượt 80 phần trăm trong 14 ngày | Lập kế hoạch mở rộng hoặc purge |
| Critical | Vượt 90 phần trăm hoặc còn ít thời gian sử dụng | Escalate và xử lý khẩn |

## 6. Quy trình forecast

1. Thu thập dữ liệu tối thiểu 30 ngày, tốt hơn là 90 ngày.
2. Tính growth rate theo ngày và theo tuần.
3. Loại bỏ spike bất thường nếu đã có nguyên nhân rõ ràng.
4. Dự báo ngày chạm ngưỡng watch, warning, critical.
5. Xác định nguyên nhân tăng trưởng: data, log, index, temp, backup hoặc workload.
6. Tạo action item: mở rộng, archive, purge, partition, optimize hoặc thay đổi retention.

## 7. Báo cáo capacity tối thiểu

1. Top database tăng trưởng nhanh nhất.
2. Top instance gần cạn disk.
3. Top transaction log hoặc WAL tăng bất thường.
4. Top temp space usage.
5. Backup storage usage và forecast.
6. Rủi ro capacity trong 30 ngày tới.
7. Khuyến nghị hành động và owner.

## 8. Checklist quản lý capacity

| Câu hỏi kiểm tra | Kết quả |
|---|---|
| Có đủ dữ liệu lịch sử để forecast không | Pass hoặc Fail |
| Có phân biệt tăng trưởng data, log, index, backup không | Pass hoặc Fail |
| Có owner cho từng rủi ro capacity không | Pass hoặc Fail |
| Có action item trước khi chạm critical không | Pass hoặc Fail |
| Có theo dõi hiệu quả sau khi xử lý không | Pass hoặc Fail |

## 9. Tài liệu liên quan

1. STD-018 Database Monitoring Standard.
2. RBK-043 Capacity Report.
3. RSK-054 Risk Register.

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
