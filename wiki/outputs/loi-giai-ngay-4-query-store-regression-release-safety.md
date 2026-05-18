---
id: loi-giai-ngay-4-query-store-regression-release-safety
title: "Lời giải ngày 4: Query Store, regression và release safety"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
  - concepts/sql-server-query-store-analysis
  - concepts/sql-server-performance-diagnostics
  - concepts/dba-policy-sop-runbook-layering
---

# Lời giải ngày 4: Query Store, regression và release safety

**Tham chiếu từ runbook chính:** [[outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba]]

File này là lời giải chi tiết cho **Ngày 4 - Query Store, plan regression và release safety** trong runbook chính. Khi học hoặc chấm bài, dùng runbook chính để xác định mục tiêu, checklist và deliverable; dùng file này để xem cách làm kỹ lưỡng, evidence mẫu và lỗi thường gặp.

## Mục tiêu lời giải

Ngày 4 đạt chuẩn khi người học dùng Query Store để nối được ba thứ: query nào bị ảnh hưởng, plan nào thay đổi và change/release nào liên quan.

## Ý nghĩa thực hành

Query Store là "hộp đen" giúp DBA nhìn lại lịch sử query, plan và runtime. Trong thực tế, rất nhiều sự cố hiệu năng không xảy ra khi DBA đang nhìn màn hình. App release xong 30 phút sau CPU tăng, API chậm, user báo lỗi. Nếu không có Query Store, DBA chỉ thấy trạng thái hiện tại và phải đoán. Nếu dùng Query Store tốt, DBA có thể so sánh trước/sau release, xác định query_id, plan_id, duration, CPU, reads và plan regression.

Bài này thực dụng vì nó dạy cách ra quyết định mitigation. Force plan, update statistics, tạo index, rollback release hay scale tài nguyên đều có rủi ro. Junior DBA không nên chọn theo cảm tính; phải dựa vào evidence: plan nào tốt hơn, time window nào thay đổi, release nào liên quan, regression do plan hay do blocking/resource saturation. Đây là kỹ năng rất thực tế khi hỗ trợ change/release.

Với Azure SQL, Query Store càng quan trọng vì nó là công cụ trọng tâm để xem workload và plan history. Người học cần hiểu rằng force plan là mitigation tạm thời, không phải chữa gốc. Sau khi giảm impact, vẫn cần RCA: vì sao optimizer chọn plan xấu, query/code/index/statistics có vấn đề gì, release checklist cần bổ sung gì.

## Tình huống thực tế tương ứng

| Tình huống | Bài thực hành giúp gì |
|---|---|
| API chậm sau release | So sánh Query Store trước/sau release. |
| Plan regression | Xác định plan tốt/xấu và cân nhắc force plan tạm. |
| CPU tăng đột biến | Tìm top CPU query trong time window. |
| Reads tăng mạnh | Phát hiện access path đổi xấu. |
| CAB hỏi release có an toàn không | Có release safety checklist và monitoring window. |

## Kỹ năng thực dụng được rèn

- Dùng Query Store theo query_id/plan_id.
- So sánh plan trước/sau theo time window.
- Phân biệt duration, CPU, logical reads và executions.
- Liên hệ regression với release/change.
- Chọn mitigation có rollback.
- Không nhầm mitigation với root cause fix.

## Dấu hiệu làm sai bài

- Chỉ lấy top query hiện tại, không có time window.
- Force plan không có lý do và rollback.
- Không kiểm tra blocking/resource saturation.
- Kết luận release gây lỗi chỉ vì xảy ra gần thời điểm release, không có evidence.
- Không ghi risk của force plan.
- Đóng incident mà không tạo follow-up RCA.

## Đáp án mong đợi

| Hạng mục | Đáp án đạt chuẩn |
|---|---|
| Query Store evidence | Có query_id, plan_id, duration, CPU, reads, executions. |
| Time window | So sánh trước/sau theo thời điểm release hoặc incident. |
| Plan comparison | Nêu khác biệt access path, join, row estimate, warnings. |
| Mitigation | Chọn option có lý do, risk và rollback. |
| Release safety | Có checklist trước/sau change. |

## Bước 1 - Tìm top query

```sql
SELECT TOP (20)
    q.query_id,
    p.plan_id,
    qt.query_sql_text,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.avg_logical_io_reads,
    rs.count_executions,
    rs.last_execution_time
FROM sys.query_store_query_text AS qt
JOIN sys.query_store_query AS q ON qt.query_text_id = q.query_text_id
JOIN sys.query_store_plan AS p ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats AS rs ON p.plan_id = rs.plan_id
ORDER BY rs.avg_duration DESC;
```

Đáp án đạt:

```text
Query_id 42 có avg_duration cao nhất trong window sau release. Cần so sánh với cùng query_id trước release và kiểm tra plan_id.
```

Không đạt:

```text
Query đầu bảng chậm nhất nên chắc là root cause.
```

## Bước 2 - So sánh plan

Người học phải lập bảng:

| Aspect | Plan tốt | Plan xấu |
|---|---|---|
| Access path | Index seek | Index/table scan |
| Logical reads | Thấp | Cao |
| Join | Nested loops hợp lý | Hash join lớn hoặc lookup nhiều |
| Estimate vs actual | Gần nhau | Lệch nhiều |
| Warning | Không có | Spill/implicit conversion/missing index |

Ý nghĩa:

- Reads tăng mạnh thường cho thấy access path đổi xấu.
- CPU tăng mạnh có thể do xử lý nhiều row, sort/hash, parallelism.
- Elapsed tăng nhưng CPU không tăng có thể do wait/lock/IO/network.
- Plan tốt cũ không phải lúc nào cũng nên force vĩnh viễn.

## Bước 3 - Decision tree mitigation

| Evidence | Mitigation hợp lý |
|---|---|
| Release đổi SQL và plan xấu xuất hiện ngay sau đó | Rollback release hoặc hotfix query. |
| Plan cũ ổn định, plan mới regression rõ | Force plan tạm thời, sau đó RCA. |
| Estimate lệch do statistics cũ | Update statistics có kiểm soát. |
| Query thiếu index thật sự | Tạo change request cho index. |
| Blocking là nguyên nhân elapsed tăng | Không tune index trước; xử lý blocking. |
| Resource tier bão hòa | Tune query hoặc scale tạm có approval. |

## Bước 4 - Force plan chỉ là mitigation

Ví dụ lệnh, chỉ dùng khi đã có plan_id rõ và approval phù hợp:

```sql
EXEC sys.sp_query_store_force_plan
    @query_id = 42,
    @plan_id = 1001;
```

Rollback:

```sql
EXEC sys.sp_query_store_unforce_plan
    @query_id = 42,
    @plan_id = 1001;
```

Evidence bắt buộc:

- Vì sao plan 1001 tốt hơn.
- Rủi ro nếu data distribution đổi.
- Monitoring window sau force plan.
- Ticket/change hoặc incident approval.

## Regression dossier mẫu

```markdown
# Query Store Regression Dossier

## Time window
- Before: 09:00-10:00
- After: 10:00-11:00
- Related change: Release v2.3.1

## Evidence
| Query ID | Plan ID | Avg duration | Avg CPU | Avg reads | Executions |
|---|---:|---:|---:|---:|---:|
| 42 | 1001 | 40 ms | 8 ms | 12 | 500 |
| 42 | 1008 | 900 ms | 320 ms | 18500 | 500 |

## Analysis
Plan 1008 chuyển sang scan trên Orders, reads tăng mạnh. Release v2.3.1 thay đổi predicate nên plan regression có tương quan thời gian.

## Decision
- Immediate mitigation: force plan 1001 trong incident window.
- Follow-up: review query predicate và index.
- Risk: force plan có thể không phù hợp nếu parameter/data thay đổi.
- Rollback: unforce plan.
```

## Release safety đáp án mẫu

Trước change:

- Có query/DDL/data migration script.
- Có rollback.
- Có test trên data đủ lớn.
- Có Query Store baseline.
- Có app owner smoke test.
- Có monitoring window.

Sau change:

- Kiểm tra top duration/CPU/reads.
- Kiểm tra error rate/API latency.
- Kiểm tra Hikari pending.
- Kiểm tra blocking/deadlock.
- Không đóng change trước khi hết monitoring window.

## Lỗi thường gặp

- Force plan không có rollback.
- Nhầm top query với root cause.
- Không so sánh time window.
- Không kiểm tra release timeline.
- Tune index khi nguyên nhân thật là blocking.

## Chấm điểm gợi ý

| Hạng mục | Điểm tối đa |
|---|---:|
| Query Store evidence | 25 |
| Plan comparison | 25 |
| Mitigation decision | 20 |
| Release safety | 15 |
| Risk/rollback | 15 |
