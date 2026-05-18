---
id: loi-giai-ngay-5-backup-restore-azure-sql
title: "Lời giải ngày 5: Backup và restore cho Azure SQL Database và Managed Instance"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
  - sources/runbook-backup-verification
  - sources/runbook-restore-validation
  - concepts/sql-server-backup-restore-operations
  - concepts/restore-validation-runbook
  - concepts/azure-sql-database
  - concepts/azure-sql-managed-instance
---

# Lời giải ngày 5: Backup và restore cho Azure SQL Database và Managed Instance

**Tham chiếu từ runbook chính:** [[outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba]]

File này là lời giải chi tiết cho **Ngày 5 - Backup/restore Azure SQL Database và Managed Instance** trong runbook chính. Khi học hoặc chấm bài, dùng runbook chính để xác định mục tiêu, checklist và deliverable; dùng file này để xem cách làm kỹ lưỡng, evidence mẫu và lỗi thường gặp.

## Mục tiêu

Bài này bổ sung phần còn thiếu trong chương trình 7 ngày: thực hành backup/restore ở mức junior DBA cho hai target cloud:

1. **Azure SQL Database**: trọng tâm là automated backups, point-in-time restore, long-term retention nếu có, restore sang database mới và validation.
2. **Azure SQL Managed Instance**: trọng tâm là automated backups/PITR, restore database trong MI, và với migration từ SQL Server có thể dùng native backup/restore khi phù hợp.

Mục tiêu không phải "bấm restore xong", mà là chứng minh database có thể khôi phục bằng evidence: recovery point, restore target, dữ liệu, schema, security, app smoke test và RPO/RTO thực tế.

## Ý nghĩa thực hành

Backup chỉ có giá trị khi restore được. Trong production, nhiều tổ chức có job backup thành công mỗi ngày nhưng chưa từng restore test, đến khi sự cố mới phát hiện backup thiếu log chain, thiếu quyền truy cập, thiếu key, restore quá lâu hoặc app không chạy được sau restore. Junior DBA phải hiểu rằng backup status là tín hiệu, còn restore validation mới là bằng chứng mạnh.

Với Azure SQL Database và Managed Instance, nhiều backup là automated backup do platform quản lý, nhưng DBA vẫn phải biết: retention là bao lâu, restore được tới thời điểm nào, restore sang đâu, ai approve, restore xong kiểm tra gì, và khi nào phải escalation nếu RPO/RTO không đạt.

## Phân biệt backup/restore theo target

| Chủ đề | Azure SQL Database | Azure SQL Managed Instance |
|---|---|---|
| Automated backup | Platform quản lý, hỗ trợ point-in-time restore theo retention. | Platform quản lý, hỗ trợ point-in-time restore theo retention. |
| Restore target | Thường restore thành database mới trên logical server. | Restore thành database trong managed instance. |
| Native backup/restore | Không giống SQL Server truyền thống cho single DB. | Hỗ trợ nhiều kịch bản gần SQL Server hơn, gồm restore từ backup phù hợp. |
| Login/user | Contained user hoặc Entra principal cần validate. | Login ở instance và user trong DB cần validate. |
| Use case lab | PITR sang DB mới, validation rồi cleanup. | PITR/restore trong MI, validation login-user mapping. |

## Lab A - Azure SQL Database point-in-time restore

### Bước A1 - Ghi thông tin trước restore

```markdown
# LAB-BR-A - Azure SQL Database PITR

## Scope
- Target type: Azure SQL Database
- Logical server:
- Source database: SalesAppDB
- Restore database: SalesAppDB_RestoreTest_YYYYMMDD
- Requested recovery time UTC:
- Owner:
```

### Bước A2 - Pre-restore evidence

Chạy trên source:

```sql
SELECT
    DB_NAME() AS database_name,
    SYSUTCDATETIME() AS captured_at_utc;

SELECT
    COUNT_BIG(*) AS orders_count
FROM dbo.Orders;

SELECT
    COUNT_BIG(*) AS customers_count
FROM dbo.Customers;
```

Ghi thêm trong Azure Portal:

- Backup retention hiện tại.
- Earliest restore point nếu Portal hiển thị.
- Requested restore point.
- Source database status.

### Bước A3 - Thực hiện restore

Trong Azure Portal:

1. Vào database `SalesAppDB`.
2. Chọn **Restore**.
3. Chọn restore point.
4. Đặt tên database mới, ví dụ `SalesAppDB_RestoreTest_20260518`.
5. Review compute/storage để tránh chi phí ngoài ý muốn.
6. Create.

CLI hoặc automation có thể dùng nếu tổ chức cho phép, nhưng phải lưu command và output đã che thông tin nhạy cảm.

### Bước A3.1 - Evidence bắt buộc trong Azure Portal

| Portal blade | Cần ghi | Vì sao quan trọng |
|---|---|---|
| Overview của source database | Status, server, tier/storage. | Xác nhận đang restore đúng database và hiểu chi phí. |
| Backups hoặc Restore | Retention, earliest restore point, restore point đã chọn. | Chứng minh RPO và khả năng PITR. |
| Restore form | Restore database name, target server, compute/storage. | Tránh ghi đè nhầm và tránh tạo tài nguyên quá đắt. |
| Notifications/Activity log | Start/end/status của restore operation. | Đo RTO và có evidence nếu restore fail. |
| Overview của restored database | Status online, server, connection endpoint. | Xác nhận restore target sẵn sàng để validate. |

```markdown
## Portal restore evidence
- Source database:
- Source status:
- Backup retention:
- Earliest restore point:
- Requested restore point UTC:
- Restore database:
- Restore target server/MI:
- Restore operation start:
- Restore operation end:
- Activity log status:
- Cleanup owner/date:
```

### Bước A4 - Validate database restore

Kết nối vào database restore:

```sql
SELECT
    DB_NAME() AS database_name,
    SYSUTCDATETIME() AS validated_at_utc;

SELECT
    COUNT_BIG(*) AS orders_count
FROM dbo.Orders;

SELECT
    COUNT_BIG(*) AS customers_count
FROM dbo.Customers;

SELECT
    type_desc,
    COUNT(*) AS object_count
FROM sys.objects
WHERE is_ms_shipped = 0
GROUP BY type_desc
ORDER BY type_desc;
```

Pass nếu:

- Restore database online.
- Row count hợp lý theo restore point.
- Object count hợp lý.
- App user hoặc validation user kết nối được.
- Không có permission issue cho smoke test.

Không pass nếu:

- Không biết restore point.
- Restore xong nhưng không validate dữ liệu.
- Row count lệch mà không giải thích.
- Không test quyền/app access.

## Lab B - Azure SQL Managed Instance restore validation

### Bước B1 - Ghi scope

```markdown
# LAB-BR-B - Azure SQL Managed Instance Restore Validation

## Scope
- Target type: Azure SQL Managed Instance
- Managed instance:
- Source database:
- Restore database:
- Recovery point:
- VNet/app test host:
- Owner:
```

### Bước B2 - Pre-check trên MI

```sql
SELECT
    @@SERVERNAME AS instance_name,
    name,
    state_desc,
    recovery_model_desc,
    create_date
FROM sys.databases
WHERE name = 'SalesAppDB_MI';
```

Nếu restore từ backup SQL Server vào MI, cần có thêm:

- Backup file location.
- Credential/access tới storage.
- CHECKSUM/VERIFYONLY nếu có.
- Compatibility concern.

### Bước B3 - Restore hoặc PITR theo MI workflow

Tùy môi trường:

| Mode | Khi dùng |
|---|---|
| PITR trong Azure Portal | Test khả năng restore platform-managed backup. |
| Native restore từ `.bak` | Migration/lab từ SQL Server vào MI nếu được hỗ trợ và cấu hình đủ. |
| DMS | Migration rehearsal có điều phối. |

Điều bắt buộc là restore sang database mới hoặc môi trường test trước, không ghi đè production khi chưa có approval.

### Bước B4 - Validate MI restore

```sql
USE SalesAppDB_MI_RestoreTest;
GO

SELECT
    DB_NAME() AS database_name,
    SUSER_SNAME() AS login_name,
    USER_NAME() AS database_user,
    SYSUTCDATETIME() AS validated_at_utc;

SELECT
    type_desc,
    COUNT(*) AS object_count
FROM sys.objects
WHERE is_ms_shipped = 0
GROUP BY type_desc
ORDER BY type_desc;
```

Kiểm tra login-user mapping:

```sql
SELECT
    dp.name AS database_user,
    dp.type_desc,
    dp.authentication_type_desc
FROM sys.database_principals AS dp
WHERE dp.name NOT IN ('dbo', 'guest', 'INFORMATION_SCHEMA', 'sys');
```

Pass nếu:

- Database online.
- Object/data validation đạt.
- Login-user mapping rõ.
- App smoke test từ network hợp lệ pass.
- Restore duration được ghi để so RTO.

## RPO/RTO trong bài lab

| Khái niệm | Cách đo trong lab |
|---|---|
| RPO | Khoảng cách giữa restore point và thời điểm sự cố giả lập. |
| RTO | Thời gian từ lúc bắt đầu restore đến khi validation pass. |

Ví dụ:

```markdown
## RPO/RTO result
- Requested restore point: 2026-05-18 08:30 UTC
- Incident time giả lập: 2026-05-18 08:35 UTC
- RPO thực tế: khoảng 5 phút
- Restore start: 09:00 UTC
- Validation pass: 09:28 UTC
- RTO thực tế: 28 phút
```

## Restore validation matrix

| Check | Azure SQL Database | Managed Instance | Status |
|---|---|---|---|
| Database online | Required | Required | |
| Row count | Required | Required | |
| Object count | Required | Required | |
| Permission/app user | Contained user/Entra | Login-user mapping/Entra | |
| App smoke test | Required | Required | |
| Performance smoke test | Recommended | Recommended | |
| Cleanup restore copy | Required nếu lab | Required nếu lab | |

## Bài thực hành hoàn chỉnh ngày 5

### Kịch bản 1 - Khôi phục nhầm xóa dữ liệu bằng PITR

Giả lập tình huống: application hoặc người vận hành xóa nhầm dữ liệu. Người học không sửa trực tiếp trên production mà restore sang database mới để lấy bằng chứng và so sánh dữ liệu.

```markdown
# Scenario D5-S1 - Accidental delete recovery

## Incident context
- Incident time UTC:
- Table impacted:
- Expected restore point:
- Business owner:

## Recovery target
- Source database:
- Restore database:
- Restore point UTC:
- Data to validate:
```

Các bước:

1. Ghi lại thời điểm sự cố giả lập.
2. Chọn restore point trước sự cố.
3. Restore sang database mới.
4. So sánh row count hoặc sample key giữa source hiện tại và database restore.
5. Ghi cách lấy lại dữ liệu: export/import có kiểm soát, script merge, hoặc escalation cho senior DBA.

Không yêu cầu junior DBA tự merge dữ liệu vào production nếu chưa có approval. Mục tiêu của bài là chứng minh backup có thể dùng được và biết cách tạo evidence cho bước phục hồi.

### Kịch bản 2 - Restore test định kỳ

Giả lập tình huống: DBA chạy kiểm tra định kỳ để đảm bảo backup có thể restore.

```markdown
# Scenario D5-S2 - Scheduled restore test

## Test window
- Planned start:
- Planned end:
- Environment:
- Cost owner:

## Pass criteria
- Restore online:
- Object count validated:
- Row count validated:
- App smoke test passed:
- Cleanup completed:
```

Checklist thực hiện:

- Kiểm tra retention và restore point.
- Restore sang tên database có hậu tố `_RestoreTest_YYYYMMDD`.
- Chạy validation schema/data/security.
- Chạy smoke test bằng app hoặc tài khoản validation.
- Ghi restore duration.
- Cleanup tài nguyên restore test sau khi có evidence.

### Kịch bản 3 - Managed Instance login-user validation

Managed Instance gần SQL Server truyền thống hơn, nên bài thực hành cần nhấn mạnh phần login-user mapping.

```sql
SELECT
    dp.name AS database_user,
    dp.type_desc,
    dp.authentication_type_desc,
    dp.default_schema_name
FROM sys.database_principals AS dp
WHERE dp.name NOT IN ('dbo', 'guest', 'INFORMATION_SCHEMA', 'sys')
ORDER BY dp.name;
```

Nếu app login fail sau restore, người học phải ghi:

- Login nào fail.
- Database user tương ứng có tồn tại không.
- Authentication model là SQL login, contained user hay Microsoft Entra.
- Lỗi xảy ra ở bước login, permission hay network.
- Người cần escalation nếu phải sửa login/user mapping.

## Run sheet thao tác ngày 5

```markdown
# Day 5 Backup/Restore Run Sheet

## 1. Scope
- Target:
- Source database:
- Restore database:
- Restore mode:
- Restore point:

## 2. Pre-check
- Source online:
- Retention known:
- Restore point selected:
- Approval/cost checked:

## 3. Execute
- Start time:
- End time:
- Status:
- Error if any:

## 4. Validate
- Database online:
- Object count:
- Row count:
- Security/app smoke test:
- RPO:
- RTO:

## 5. Close
- Pass/Fail:
- Cleanup:
- Evidence location:
- Lesson learned:
```

## Evidence dossier mẫu

```markdown
# Backup Restore Evidence

## Scope
- Ticket:
- Target type: Azure SQL Database / Azure SQL Managed Instance
- Source database:
- Restore database:
- Restore point:
- Operator:

## Pre-check
| Check | Result | Evidence |
|---|---|---|
| Source online | | |
| Retention known | | |
| Recovery point selected | | |
| Approval if required | | |

## Restore execution
| Step | Start | End | Status |
|---|---|---|---|
| Start restore | | | |
| Database online | | | |
| Validation | | | |

## Validation
| Check | Expected | Actual | Status |
|---|---|---|---|
| Row count | | | |
| Object count | | | |
| App login | | | |
| App smoke test | | | |

## RPO/RTO
- RPO:
- RTO:

## Decision
- Pass/Fail:
- Known issues:
- Cleanup:
```

## Lỗi thường gặp

- Nhầm automated backup với restore readiness.
- Không biết retention và restore point.
- Restore xong không kiểm tra quyền/app.
- Không đo RTO.
- Không cleanup database restore test.
- Với MI, quên kiểm tra login-user mapping.
- Với Azure SQL Database, quên contained user/auth model.

## Chấm điểm gợi ý

| Hạng mục | Điểm tối đa |
|---|---:|
| Scope và restore point rõ | 15 |
| Pre-check/retention/RPO rõ | 20 |
| Restore execution evidence | 20 |
| Validation matrix đầy đủ | 30 |
| Cleanup và lessons learned | 15 |
