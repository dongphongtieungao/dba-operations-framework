---
doc_id: RBK-040
title: "Runbook Add Replica"
doc_type: runbook
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

# RBK-040 - Runbook Add Replica

## 1. Mục đích

Tự động hóa bổ sung replica.

## 2. Mô tả tóm tắt

Playbook thêm replica mới, cấu hình replication, kiểm tra lag, thêm monitoring, thêm alert và cập nhật inventory.

## 3. Phạm vi áp dụng

Tài liệu áp dụng cho hoạt động DBA trong môi trường production, staging và các môi trường non production có dữ liệu hoặc cấu hình quan trọng. Khi triển khai đa DBMS, nội dung trong tài liệu này là baseline chung. Các khác biệt riêng của SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL hoặc Z DB phải được mô tả trong phụ lục vận hành tương ứng.


## 4. Nguyên tắc thiết kế runbook

1. Runbook phải idempotent ở mức có thể, hoặc phải kiểm tra trạng thái trước khi thay đổi.
2. Không hard code credential, secret hoặc thông tin nhạy cảm trong script.
3. Mọi input phải được validate trước khi thực thi.
4. Script phải có chế độ dry run nếu thao tác có rủi ro cao.
5. Script phải xuất output có cấu trúc để lưu evidence và tích hợp ITSM.
6. Thao tác production phải dùng service account được phê duyệt.


## 4. Input bắt buộc

| Input | Mô tả |
|---|---|
| ticket_id | Mã yêu cầu thêm replica |
| source_instance | Primary hoặc source hiện tại |
| target_instance | Replica mới hoặc node mới |
| dbms_type | SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| replication_mode | Sync, async, log shipping, streaming, Data Guard hoặc mô hình nền tảng |
| rpo_requirement | Yêu cầu RPO |
| network_requirement | Port, firewall, routing |
| monitoring_target | Dashboard và alert cần thêm |

## 5. Điều kiện tiên quyết

1. Source instance ổn định và không có corruption.
2. Backup hoặc seed data sẵn sàng.
3. Network giữa source và target đã mở.
4. Target có đủ CPU, memory, disk và phiên bản tương thích.
5. Có approval nếu thay đổi production topology.

## 6. Luồng tự động hóa

1. Validate input và kiểm tra approval.
2. Kiểm tra version compatibility.
3. Kiểm tra disk và network trên target.
4. Chuẩn bị backup, snapshot hoặc seed data.
5. Khởi tạo replica.
6. Cấu hình replication user hoặc endpoint.
7. Bắt đầu đồng bộ.
8. Kiểm tra replication lag.
9. Thêm monitoring và alert.
10. Cập nhật inventory, topology diagram và ticket evidence.

## 7. Post check

| Kiểm tra | Kết quả mong muốn |
|---|---|
| Replica status | Running hoặc synchronized theo mô hình |
| Lag | Trong ngưỡng RPO |
| Read only | Đúng trạng thái theo thiết kế |
| Backup | Không làm hỏng backup chain |
| Monitoring | Có dashboard và alert |
| Inventory | Topology được cập nhật |

## 8. Rollback

1. Stop replication trên target.
2. Remove target khỏi replication topology.
3. Revoke replication credential nếu đã tạo mới.
4. Remove monitoring object và alert.
5. Cập nhật inventory trạng thái rollback.

## 9. Tài liệu liên quan

1. SOP-027 Replication Troubleshooting.
2. SOP-028 HA Failover and Switchover.
3. STD-018 Monitoring Standard.

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
