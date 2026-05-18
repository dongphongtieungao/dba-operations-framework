---
id: loi-giai-ngay-2-io-execution-plan-index-review
title: "Lời giải ngày 2: IO, execution plan và index review"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
  - concepts/sql-server-execution-plan-analysis
  - concepts/sql-server-index-and-statistics-maintenance
  - concepts/sql-server-query-store-analysis
---

# Lời giải ngày 2: IO, execution plan và index review

**Tham chiếu từ runbook chính:** [[outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba]]

File này là lời giải chi tiết cho **Ngày 2 - IO, execution plan và index review** trong runbook chính. Khi học hoặc chấm bài, dùng runbook chính để xác định mục tiêu, checklist và deliverable; dùng file này để xem cách làm kỹ lưỡng, evidence mẫu và lỗi thường gặp.

## Mục tiêu lời giải

Ngày 2 đạt chuẩn khi người học biết biến câu "query chậm" thành phân tích có evidence: logical reads, CPU, elapsed time, actual plan, rủi ro index và rollback.

## Ý nghĩa thực hành

Tuning query là nơi developer chuyển sang DBA dễ mắc sai lầm nhất. Developer thường nhìn query theo logic nghiệp vụ: đúng dữ liệu chưa, trả về nhanh trên máy dev chưa. DBA phải nhìn query theo chi phí vận hành: đọc bao nhiêu page, dùng plan nào, có scan bảng lớn không, có làm chậm write workload không, có rủi ro plan regression không.

Bài này thực dụng vì nó ép người học đi từ cảm giác "query chậm" sang bằng chứng đo được. `STATISTICS IO` cho thấy SQL Server phải đọc bao nhiêu data page; execution plan cho thấy engine đã chọn đường đi nào; index review giúp tránh tạo index trùng hoặc index chỉ tốt cho một query nhưng làm hại workload ghi. Đây là khác biệt giữa "biết SQL" và "vận hành SQL".

Trong Azure SQL, bài này càng quan trọng vì tài nguyên bị giới hạn theo tier. Một query đọc quá nhiều page có thể đẩy CPU, data IO hoặc log IO lên cao, làm ảnh hưởng cả ứng dụng. Junior DBA không cần tối ưu mọi trường hợp phức tạp, nhưng phải biết khi nào evidence đủ để đề xuất index, khi nào phải defer, và khi nào phải escalation cho DBA senior.

## Tình huống thực tế tương ứng

| Tình huống | Bài thực hành giúp gì |
|---|---|
| API `GetCustomerOrders` chậm | Biết đo reads/CPU/elapsed và xem plan thay vì đoán thiếu index. |
| Azure SQL CPU cao | Tìm top query và phân biệt CPU cao do scan/sort/hash hay nguyên nhân khác. |
| Release thêm query mới | Review plan trước production change. |
| Missing index suggestion xuất hiện | Biết coi đó là tín hiệu, không phải mệnh lệnh. |
| Index mới làm write chậm | Biết đánh giá write overhead và rollback. |

## Kỹ năng thực dụng được rèn

- Đọc logical reads như chỉ số chi phí data access.
- Phân biệt CPU time và elapsed time.
- Nhận diện scan, seek, key lookup, sort, hash, spill, implicit conversion.
- Review index hiện có trước khi tạo index mới.
- Lập change record cho index.
- Đo before/after và đưa ra quyết định apply/defer/reject.

## Dấu hiệu làm sai bài

- Tạo index ngay sau khi thấy query chậm.
- Không so sánh before/after.
- Chỉ test một parameter không đại diện.
- Không kiểm tra index trùng hoặc overlapping.
- Không nêu rủi ro write/storage.
- Không có rollback script.

## Đáp án mong đợi

| Hạng mục | Đáp án đạt chuẩn |
|---|---|
| Query identity | Biết query/procedure/API endpoint, Query Store query_id nếu có. |
| Before metric | Có logical reads, CPU, elapsed, executions. |
| Plan reading | Nhận ra scan/seek/key lookup/sort/hash/spill/implicit conversion. |
| Index decision | Có ít nhất 2 option, nêu benefit/risk. |
| After metric | Có so sánh trước/sau. |
| Change safety | Có rollback script và post-change monitoring. |

## Bước 1 - Đo IO và time

Query:

```sql
USE SalesAppDB;
GO

SET STATISTICS IO ON;
SET STATISTICS TIME ON;

EXEC dbo.GetCustomerOrders @CustomerId = 12345;

SET STATISTICS IO OFF;
SET STATISTICS TIME OFF;
```

Cách đọc đáp án:

| Metric | Cách hiểu |
|---|---|
| Logical reads cao | Query phải xử lý nhiều page trong buffer pool. |
| Physical reads > 0 | Có đọc từ storage, nhưng không phải lúc nào cũng là root cause. |
| CPU time cao gần elapsed | CPU-bound. |
| Elapsed cao nhưng CPU thấp | Có thể đang chờ IO, lock, memory, network hoặc worker. |

Kết luận chưa đạt:

```text
Query chậm nên thêm index.
```

Kết luận đạt:

```text
Query GetCustomerOrders trả về ít dòng nhưng logical reads cao. Actual plan cho thấy scan trên Orders và sort theo CreatedAt. Giả thuyết chính: thiếu index hỗ trợ predicate CustomerId và order by CreatedAt DESC. Cần kiểm tra index hiện có trước khi đề xuất.
```

## Bước 2 - Review index hiện có

```sql
SELECT
    t.name AS table_name,
    i.name AS index_name,
    i.type_desc,
    i.is_primary_key,
    i.is_unique,
    c.name AS column_name,
    ic.key_ordinal,
    ic.is_included_column
FROM sys.tables AS t
JOIN sys.indexes AS i ON t.object_id = i.object_id
JOIN sys.index_columns AS ic ON i.object_id = ic.object_id AND i.index_id = ic.index_id
JOIN sys.columns AS c ON ic.object_id = c.object_id AND ic.column_id = c.column_id
WHERE t.name IN ('Orders', 'Customers', 'PaymentEvents')
ORDER BY t.name, i.index_id, ic.key_ordinal, ic.index_column_id;
```

Đọc kết quả:

- Nếu chỉ có clustered primary key trên `OrderId`, query theo `CustomerId` khó seek tốt.
- Nếu đã có index `(CustomerId)` nhưng query vẫn lookup nhiều, cân nhắc covering index.
- Nếu đã có index tương tự, không tạo index trùng.

## Bước 3 - Option analysis

Option mẫu:

| Option | Benefit | Risk | Decision |
|---|---|---|---|
| Không đổi | Không tăng write/storage | Query vẫn đọc nhiều | Reject nếu evidence chậm rõ |
| Index `(CustomerId, CreatedAt DESC)` INCLUDE fields | Giảm reads, giảm sort/key lookup | Tăng storage/write overhead | Test trong lab |
| Rewrite query | Có thể giảm data trả về | Cần app change | Defer nếu index đủ |
| Force plan | Mitigation tạm nếu regression | Không giải quyết thiết kế index | Chỉ dùng khi có plan cũ tốt |

## Bước 4 - Tạo index trong lab

```sql
CREATE INDEX IX_Orders_CustomerId_CreatedAt
ON dbo.Orders (CustomerId, CreatedAt DESC)
INCLUDE (OrderStatus, TotalAmount);
```

Rollback:

```sql
DROP INDEX IX_Orders_CustomerId_CreatedAt ON dbo.Orders;
```

## Bước 5 - Đo lại

Chạy lại cùng query, cùng parameter, ghi bảng:

| Metric | Before | After | Kết luận |
|---|---:|---:|---|
| Logical reads | Cao | Giảm mạnh | Index có tác dụng đọc |
| CPU ms | Cao/vừa | Giảm | Tốt |
| Elapsed ms | Cao | Giảm | Tốt |
| Main operator | Scan/sort/lookup | Seek | Tốt |

Nếu after không tốt:

- Kiểm tra parameter có đại diện không.
- Kiểm tra statistics.
- Kiểm tra plan có dùng index mới không.
- Kiểm tra index có đúng thứ tự cột không.
- Kiểm tra query có implicit conversion không.

## Query tuning dossier mẫu

```markdown
# Query Tuning Dossier

## Query identity
- Procedure: dbo.GetCustomerOrders
- API: GET /customers/{id}/orders
- Query Store query_id: 42
- Plan id before: 1001

## Before
| Metric | Value |
|---|---:|
| Logical reads | 18500 |
| CPU ms | 320 |
| Elapsed ms | 900 |

## Plan observations
- Orders index/table scan.
- Sort by CreatedAt DESC.
- Missing covering index for CustomerId + CreatedAt.

## Options
| Option | Decision |
|---|---|
| Covering index | Test and recommend |
| Query rewrite | Defer |
| No change | Reject |

## After
| Metric | Before | After |
|---|---:|---:|
| Logical reads | 18500 | 12 |
| CPU ms | 320 | 5 |
| Elapsed ms | 900 | 20 |

## Risk
- Extra storage.
- Extra write overhead on Orders.
- Need post-change monitoring.

## Rollback
DROP INDEX IX_Orders_CustomerId_CreatedAt ON dbo.Orders;
```

## Lỗi thường gặp

- Chỉ nhìn missing index suggestion.
- Không kiểm tra index trùng.
- Không đo after.
- Không nêu write overhead.
- Không có rollback.
- Chỉ test một parameter quá may mắn.

## Chấm điểm gợi ý

| Hạng mục | Điểm tối đa |
|---|---:|
| Before metric đủ | 20 |
| Đọc plan đúng | 25 |
| Option/risk rõ | 20 |
| After metric đủ | 20 |
| Rollback/post-check | 15 |
