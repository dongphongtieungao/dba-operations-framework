---
id: runbook-capacity-report
title: Runbook Capacity Report
type: source
created: 2026-05-18
updated: 2026-05-18
authors:
  - DBA Team
year: 2026
importance: 3
urls: []
raw_paths:
  - raw/sources/dba_framework/document_guide/43_runbook_capacity_report.md
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: skipped
findings: []
---

## Summary

Runbook Capacity Report chuẩn hóa cách tạo báo cáo dung lượng cho database estate theo ngày, tuần hoặc tháng. Tài liệu tập trung vào kích thước database, tốc độ tăng trưởng data/log, đỉnh sử dụng temp, backup size, dung lượng đĩa còn trống, dự báo ngày chạm ngưỡng và danh sách đối tượng tăng trưởng nhanh. Điểm tối ưu của runbook là nối metric capacity với ticket hành động, owner, risk register và báo cáo DBA định kỳ.

## Key Claims

- Capacity report phải dựa trên dữ liệu hiện tại và lịch sử tăng trưởng, không chỉ snapshot dung lượng tại một thời điểm. (confidence: high)
- Dự báo cần chỉ ra ngày chạm các ngưỡng watch, warning và critical để DBA tạo action item trước khi hết dung lượng. (confidence: high)
- Các metric trọng yếu gồm database_size, data_growth_rate, log_growth_rate, temp_usage_peak, backup_size, disk_free_percent, days_to_threshold và top_growth_objects. (confidence: high)
- Ticket nên được tạo khi dự báo vượt 80 phần trăm trong 14 ngày, vượt 90 phần trăm trong 7 ngày, log/WAL tăng bất thường, temp space vượt ngưỡng thường xuyên hoặc backup storage không đủ retention. (confidence: high)
- Báo cáo capacity cần gắn với monthly DBA report và risk register để việc mở rộng, archive, purge, partition hoặc tuning có owner rõ ràng. (confidence: high)

## Evidence

- Input bắt buộc gồm report_period, environment, dbms_type, instance_list, forecast_window_days và threshold_profile.
- Luồng thực thi yêu cầu thu thập dung lượng hiện tại, lấy lịch sử tăng trưởng từ monitoring hoặc repository, tính growth rate, forecast ngưỡng, xác định top risk và sinh báo cáo.
- Output báo cáo gồm executive summary, top risks, growth trend, forecast, recommended actions và owner.
- Điều kiện tạo ticket nối capacity forecast với các ngưỡng 80/90 phần trăm, log/WAL tăng bất thường, temp space và backup retention.

## Related Concepts

- [[concepts/database-capacity-reporting]]
- [[concepts/dba-operations-kpi-and-risk]]
- [[concepts/azure-sql-monitoring-and-observability]]
- [[concepts/sql-server-storage-and-tempdb-operations]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-policy-sop-runbook-layering]]

## Related Sources

- [[sources/dba-operations-framework]]
- [[sources/runbook-database-health-check]]
- [[sources/runbook-failover-execution]]

## People

Không có cá nhân cụ thể được tạo hồ sơ trong lần nạp này.

## Open Questions

- Cần xác định công thức forecast chuẩn cho từng loại tăng trưởng: tuyến tính, theo mùa vụ, hoặc theo release/event.
- Cần bổ sung mapping rõ giữa ngưỡng capacity và mức độ rủi ro trong risk register.
- Cần quy định cách xử lý khi số liệu lịch sử thiếu hoặc monitoring repository không đầy đủ.

## Notes
