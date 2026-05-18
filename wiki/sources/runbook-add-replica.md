---
id: runbook-add-replica
title: Runbook Add Replica
type: source
created: 2026-05-18
updated: 2026-05-18
authors:
  - DBA Team
year: 2026
importance: 4
urls: []
raw_paths:
  - raw/sources/dba_framework/document_guide/40_runbook_add_replica.md
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: skipped
findings: []
---

## Summary

Runbook Add Replica mô tả playbook bổ sung replica mới vào topology database, bao gồm kiểm tra approval, compatibility, disk, network, seed data, replication credential/endpoint, replication lag, monitoring, alert và inventory. Tài liệu coi việc thêm replica là thay đổi topology có rủi ro, cần post-check và rollback rõ ràng. Điểm cần hoàn thiện tiếp là bổ sung thao tác chi tiết theo từng công nghệ replication.

## Key Claims

- Source instance phải ổn định, không có corruption và target phải đủ tài nguyên, network, phiên bản tương thích trước khi thêm replica. (confidence: high)
- Add replica cần kiểm soát replication mode, RPO requirement, network requirement, monitoring target và approval nếu thay đổi production topology. (confidence: high)
- Post-check phải xác nhận replica running/synchronized, lag trong ngưỡng RPO, read-only đúng thiết kế, backup chain không bị hỏng, monitoring/alert và inventory đã cập nhật. (confidence: high)
- Rollback cần dừng replication, remove target khỏi topology, revoke credential, remove monitoring object/alert và cập nhật inventory. (confidence: high)
- Runbook là baseline đa DBMS; SQL Server Always On, PostgreSQL streaming replication, MySQL/MariaDB replication, Oracle Data Guard và nền tảng Z DB cần bước riêng. (confidence: medium)

## Evidence

- Input bắt buộc gồm ticket_id, source_instance, target_instance, dbms_type, replication_mode, rpo_requirement, network_requirement và monitoring_target.
- Điều kiện tiên quyết bao gồm source ổn định, backup/seed data sẵn sàng, network mở, target đủ CPU/memory/disk và version compatibility.
- Luồng tự động hóa gồm validate input/approval, kiểm tra version/disk/network, chuẩn bị seed data, khởi tạo replica, cấu hình replication user/endpoint, bắt đầu đồng bộ, kiểm tra lag, thêm monitoring/alert và cập nhật topology evidence.
- Post-check yêu cầu replica status, lag, read-only, backup chain, monitoring và inventory.

## Related Concepts

- [[concepts/database-replica-provisioning]]
- [[concepts/high-availability-and-disaster-recovery]]
- [[concepts/business-continuity]]
- [[concepts/dba-failover-execution-runbook]]
- [[concepts/azure-sql-monitoring-and-observability]]
- [[concepts/dba-evidence-driven-operations]]

## Related Sources

- [[sources/dba-operations-framework]]
- [[sources/runbook-failover-execution]]
- [[sources/runbook-backup-verification]]

## People

Không có cá nhân cụ thể được tạo hồ sơ trong lần nạp này.

## Open Questions

- Cần bổ sung playbook riêng cho SQL Server Always On, PostgreSQL, MySQL/MariaDB, Oracle Data Guard và Azure SQL geo-replication/failover group.
- Cần xác định rõ khi nào replica được phép phục vụ đọc và khi nào chỉ dùng cho HA/DR.
- Cần nối runbook với capacity report để kiểm tra target còn đủ dung lượng theo forecast, không chỉ tại thời điểm thêm replica.

## Notes

