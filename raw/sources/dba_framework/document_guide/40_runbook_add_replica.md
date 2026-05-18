---
doc_id: RBK-040
title: "Runbook Add Replica"
doc_type: runbook
implementation_order: 2
status: ready_for_review
owner: DBA Team
reviewer: DBA Lead
approver: Service Owner
version: 1.0
created_date: 2026-05-15
last_updated: 2026-05-18
language: vi
framework: DBA Operations Framework
related_documents:
  - DBA-SOP-027 SOP Replication Troubleshooting
  - DBA-SOP-028 SOP HA Failover and Switchover
  - DBA-RBK-041 Runbook Failover Execution
  - DBA-RBK-038 Runbook Backup Verification
  - DBA-STD-018 Database Monitoring Standard
  - DBA-TMP-048 Template Operation Evidence
---

# RBK-040 - Runbook Add Replica

## 1. Mục đích

Tự động hóa hoặc bán tự động hóa việc bổ sung replica vào topology database nhằm tăng khả năng sẵn sàng, hỗ trợ DR, mở rộng đọc hoặc phục vụ kiểm thử vận hành có kiểm soát.

## 2. Mô tả tóm tắt

Playbook thêm replica mới, kiểm tra source/target, chuẩn bị seed data, cấu hình replication credential hoặc endpoint, bắt đầu đồng bộ, kiểm tra replication lag, thêm monitoring/alert, cập nhật inventory/topology diagram và lưu evidence.

## 3. Phạm vi áp dụng

Áp dụng cho SQL Server Always On Availability Groups, log shipping, PostgreSQL streaming replication, MySQL/MariaDB replication, Oracle Data Guard, Azure SQL geo-replication/failover group và các nền tảng Z DB có cơ chế replica. Với production topology, mọi thay đổi phải có RFC hoặc approval tương đương.

## 4. Nguyên tắc thiết kế runbook

1. Không thêm replica nếu source chưa ổn định hoặc có dấu hiệu corruption.
2. Target phải đủ CPU, memory, storage, network và version compatibility.
3. Phải xác định rõ replica dùng cho HA/DR, read-only workload, backup offload hay test.
4. Replication credential, endpoint và firewall phải được kiểm soát theo policy.
5. Thao tác production phải có rollback plan và evidence đầy đủ.
6. Không đưa replica vào read traffic nếu chưa qua post-check và owner xác nhận.

## 5. Input bắt buộc

| Input | Mô tả |
|---|---|
| ticket_id | Mã yêu cầu thêm replica |
| source_instance | Primary hoặc source hiện tại |
| target_instance | Replica mới hoặc node mới |
| dbms_type | SQL Server, PostgreSQL, MySQL, MariaDB, Oracle, Azure SQL, Z DB |
| database_list | Danh sách database cần replicate |
| replication_mode | Sync, async, log shipping, streaming, Data Guard hoặc mô hình nền tảng |
| replica_purpose | HA, DR, read-only, backup offload, reporting |
| rpo_requirement | Yêu cầu RPO |
| network_requirement | Port, firewall, routing, private endpoint hoặc DNS |
| monitoring_target | Dashboard và alert cần thêm |
| approver | Người phê duyệt topology change |

## 6. Điều kiện tiên quyết

1. Source instance ổn định và không có corruption.
2. Backup hoặc seed data sẵn sàng.
3. Network giữa source và target đã mở.
4. Target có đủ CPU, memory, disk và phiên bản tương thích.
5. Có approval nếu thay đổi production topology.
6. Có tài khoản/credential replication được phê duyệt.
7. Có ngưỡng replication lag và alert tương ứng.
8. Có inventory và topology diagram hiện tại.

## 7. Luồng tự động hóa

1. Validate input và kiểm tra approval.
2. Kiểm tra version compatibility.
3. Kiểm tra disk, network, firewall/private endpoint trên target.
4. Kiểm tra trạng thái source, backup gần nhất và corruption signal.
5. Chuẩn bị backup, snapshot hoặc seed data.
6. Khởi tạo replica.
7. Cấu hình replication user, endpoint, listener, slot hoặc service binding.
8. Bắt đầu đồng bộ.
9. Kiểm tra replication lag và trạng thái sync.
10. Thêm monitoring và alert.
11. Kiểm tra backup chain hoặc policy không bị ảnh hưởng.
12. Cập nhật inventory, topology diagram và ticket evidence.

## 8. Post-check

| Kiểm tra | Kết quả mong muốn |
|---|---|
| Replica status | Running hoặc synchronized theo mô hình |
| Lag | Trong ngưỡng RPO |
| Read only | Đúng trạng thái theo thiết kế |
| Backup | Không làm hỏng backup chain |
| Monitoring | Có dashboard và alert |
| Inventory | Topology được cập nhật |
| Security | Credential/endpoint đúng policy |
| Failover readiness | Chỉ đánh dấu ready nếu đã kiểm tra theo yêu cầu HA/DR |

## 9. Gợi ý thao tác theo DBMS

### 9.1. SQL Server

1. Kiểm tra Availability Group health, endpoint, listener và replica role.
2. Seed database bằng automatic seeding hoặc backup/restore theo policy.
3. Kiểm tra synchronization_state, synchronization_health, log_send_queue và redo_queue.
4. Kiểm tra read-only routing nếu replica phục vụ đọc.
5. Kiểm tra SQL Agent jobs, login, linked server, credential và backup preference.

### 9.2. Azure SQL

1. Xác định dùng active geo-replication hay failover group.
2. Kiểm tra region, private endpoint, firewall, DNS và connection strategy.
3. Theo dõi replication state, secondary role, failover group endpoint và Azure Monitor alert.

### 9.3. PostgreSQL

1. Kiểm tra replication slot, WAL retention, timeline và streaming status.
2. Chuẩn bị base backup hoặc snapshot.
3. Kiểm tra replay lag và khả năng promote nếu dùng cho DR.

### 9.4. MySQL/MariaDB

1. Kiểm tra GTID/binlog, replica user, source position và read_only/super_read_only.
2. Xác nhận replication threads chạy ổn định và lag trong ngưỡng.

### 9.5. Oracle

1. Kiểm tra Data Guard broker, transport lag, apply lag và archive log.
2. Xác nhận standby role, protection mode và switchover readiness nếu có.

## 10. Điều kiện dừng

1. Source có corruption hoặc backup chain không tin cậy.
2. Target thiếu tài nguyên hoặc version không tương thích.
3. Network chưa mở hoặc DNS/private endpoint chưa sẵn sàng.
4. Không có approval cho production topology.
5. Replication lag vượt RPO ngay sau khi đồng bộ ban đầu.
6. Không thể bật monitoring hoặc alert cho replica production.

## 11. Rollback

1. Stop replication trên target.
2. Remove target khỏi replication topology.
3. Revoke replication credential nếu đã tạo mới.
4. Remove monitoring object và alert.
5. Cập nhật inventory trạng thái rollback.
6. Xóa seed data hoặc tài nguyên target nếu an toàn và đã được phê duyệt.
7. Lưu rollback evidence và action item còn lại.

## 12. Tài liệu liên quan

1. SOP-027 Replication Troubleshooting.
2. SOP-028 HA Failover and Switchover.
3. RBK-041 Failover Execution.
4. STD-018 Monitoring Standard.

## 13. Yêu cầu evidence

1. Ticket ID hoặc change ID.
2. Người yêu cầu, người thực hiện, người phê duyệt nếu có.
3. Thời gian bắt đầu và kết thúc.
4. Input đã sử dụng.
5. Output hoặc log thao tác.
6. Kết quả kiểm tra sau thực hiện.
7. Đường dẫn dashboard, report, script output hoặc artifact liên quan.
8. Replication status và lag sau khi thêm.
9. Topology diagram hoặc inventory record sau thay đổi.

## 14. Tiêu chí nghiệm thu

1. Replica được thêm vào topology đúng mục đích.
2. Lag nằm trong ngưỡng RPO hoặc có exception được duyệt.
3. Monitoring và alert đã hoạt động.
4. Inventory/topology đã cập nhật.
5. Backup chain hoặc backup policy không bị phá vỡ.
6. Evidence đã lưu và ticket được cập nhật.

## 15. Chỉ số liên quan

| KPI | Công thức tham khảo |
|---|---|
| Replica provisioning success rate | Successful add replica / Total add replica requests |
| Initial sync duration | Sync completed time - Start time |
| Replication lag compliance | Replicas within RPO / Total replicas |
| Post-change incident count | Incidents after replica changes |

## 16. Quản trị thay đổi tài liệu

Tài liệu phải được review ít nhất mỗi 6 tháng hoặc sau các sự kiện sau:

1. Có sự cố P1 hoặc P2 liên quan.
2. Có thay đổi lớn về kiến trúc database.
3. Có thay đổi về yêu cầu bảo mật, audit hoặc compliance.
4. Có thay đổi công cụ vận hành hoặc nền tảng cloud.
5. Có phát hiện sai lệch giữa tài liệu và thực tế vận hành.
