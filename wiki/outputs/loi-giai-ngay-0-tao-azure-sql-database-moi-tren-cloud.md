---
id: loi-giai-ngay-0-tao-azure-sql-database-moi-tren-cloud
title: "Lời giải ngày 0: Tạo Azure SQL Database và Azure SQL Managed Instance trên cloud"
type: output
created: 2026-05-18
updated: 2026-05-18
covers:
  - outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba
  - sources/azure-sql-documentation
  - concepts/azure-sql-database
  - concepts/azure-sql-managed-instance
  - concepts/private-endpoints
  - concepts/azure-sql-monitoring-and-observability
  - concepts/sql-server-security-auditing
---

# Lời giải ngày 0: Tạo Azure SQL Database và Azure SQL Managed Instance trên cloud

**Tham chiếu từ runbook chính:** [[outputs/runbook-thuc-hanh-7-ngay-java-developer-den-junior-azure-sql-dba]]

File này là lời giải chi tiết cho **Ngày 0 - Tạo Azure SQL target lab trên cloud** trong runbook chính. Khi học hoặc chấm bài, dùng runbook chính để xác định mục tiêu, checklist và deliverable; dùng file này để xem cách làm kỹ lưỡng, evidence mẫu và lỗi thường gặp.

## Mục tiêu lời giải

Ngày 0 là bước dựng nền tảng cloud trước khi bước vào 7 ngày vận hành. Người học phải hiểu và tạo được hai loại target Azure SQL khác nhau:

1. **Azure SQL Database**: dịch vụ PaaS ở mức database, còn gọi trong tài liệu này là nhánh **service/database-level**.
2. **Azure SQL Managed Instance**: dịch vụ PaaS ở mức instance, còn gọi trong tài liệu này là nhánh **instance-level**.

Kết quả đạt chuẩn là người học tạo được môi trường lab, kết nối được an toàn, có app user riêng, chạy smoke test, biết xem monitoring tối thiểu và có cleanup plan để tránh chi phí.

## Ý nghĩa thực hành

Trong vận hành thật, "Azure SQL" không phải một thứ duy nhất. Azure SQL Database và Azure SQL Managed Instance khác nhau về đơn vị quản lý, network, compatibility, chi phí, cách kết nối, cách cấp login/user và cách migration. Nếu người học không phân biệt được hai loại này, họ sẽ dễ chọn sai target hoặc debug sai lớp khi app không kết nối được.

Tạo tài nguyên cloud cũng không chỉ là bấm **Create**. Một database mới phải có ngữ cảnh rõ: subscription, resource group, region, server hoặc managed instance, network public/private, admin, app identity, backup retention, monitoring, owner và chi phí. Đây là nền tảng của tư duy DBA cloud.

Với Java developer chuyển sang Azure SQL DBA, bài này giúp hiểu vì sao connection string đúng chưa đủ. App có thể fail vì firewall, DNS, VNet, private endpoint, TLS, login, database user, role, HikariCP pool hoặc resource limit.

## Nguồn đối chiếu Microsoft Learn

- Quickstart tạo Azure SQL Database single database: https://learn.microsoft.com/en-us/azure/azure-sql/database/single-database-create-quickstart?view=azuresql
- Network access controls cho Azure SQL Database: https://learn.microsoft.com/en-us/azure/azure-sql/database/network-access-controls-overview
- Server-level firewall rule: https://learn.microsoft.com/en-us/azure/azure-sql/database/firewall-create-server-level-portal-quickstart?view=azuresql
- Private Link cho Azure SQL Database: https://learn.microsoft.com/en-us/azure/azure-sql/database/private-endpoint-overview?view=azuresql
- Quickstart tạo Azure SQL Managed Instance: https://learn.microsoft.com/en-us/azure/azure-sql/managed-instance/instance-create-quickstart?view=azuresql
- Connectivity architecture cho Azure SQL Managed Instance: https://learn.microsoft.com/en-us/azure/azure-sql/managed-instance/connectivity-architecture-overview?view=azuresql-mi
- Private Link cho Azure SQL Managed Instance: https://learn.microsoft.com/en-us/azure/azure-sql/managed-instance/private-endpoint-overview?view=azuresql

## Chọn loại nào?

| Tiêu chí | Azure SQL Database | Azure SQL Managed Instance |
|---|---|---|
| Đơn vị quản lý | Database riêng lẻ trên logical server. | Instance managed gần SQL Server hơn. |
| Phù hợp | App hiện đại, một database, ít phụ thuộc instance-level feature. | Migration từ SQL Server cần tương thích cao hơn. |
| SQL Agent | Không giống SQL Server Agent truyền thống. | Có SQL Agent trong phạm vi MI. |
| Cross-database | Hạn chế hơn, cần thiết kế lại tùy trường hợp. | Gần SQL Server hơn. |
| Network | Public endpoint có firewall; có thể dùng Private Link. | Đặt trong VNet/subnet dedicated; thiên về private/VNet-local. |
| Chi phí lab | Thường dễ tạo nhỏ hơn. | Thường nặng hơn, cần kiểm soát chi phí kỹ. |
| Dùng trong khóa học | Học database-level PaaS, app connectivity, firewall/private endpoint. | Học migration target gần SQL Server 2019, VNet, instance-level operations. |

Đáp án đạt chuẩn:

```text
Em tạo Azure SQL Database để hiểu database-level service và app connectivity đơn giản.
Em tạo Azure SQL Managed Instance để hiểu target migration instance-level gần SQL Server hơn.
Em không coi hai loại này là giống nhau; mỗi loại có network, security, cost và validation khác nhau.
```

## Kết quả cần đạt

| Hạng mục | Kết quả đạt chuẩn |
|---|---|
| Resource group | Có resource group riêng cho SQL Database và MI hoặc ghi rõ dùng chung lab. |
| Azure SQL Database | Có logical server, database, firewall/private access, app user, smoke test. |
| Azure SQL Managed Instance | Có MI hoặc provisioning design nếu chưa được cấp quyền/chi phí, có database, app login/user, smoke test. |
| Network | Không mở network quá rộng; hiểu public endpoint, firewall, Private Link, VNet/subnet. |
| Security | App không dùng admin; không ghi password vào evidence. |
| Monitoring | Biết xem metric và trạng thái cơ bản cho cả hai loại. |
| Cleanup | Có ngày xóa hoặc review tài nguyên. |

## Azure Portal map ngày 0

Người học phải biết dùng Portal như một bảng điều khiển vận hành, không chỉ như nơi bấm **Create**.

| Portal area | Track A - Azure SQL Database | Track B - Managed Instance | Evidence cần ghi |
|---|---|---|---|
| Resource group | Tài nguyên server/database nằm đúng RG. | MI, VNet/subnet và tài nguyên liên quan nằm đúng RG. | Subscription, RG, region, owner/tag. |
| Overview | Server/database name, status, FQDN. | MI name, status, FQDN. | Target name/FQDN, status, SKU/tier. |
| Networking | Firewall rule, public access, private endpoint nếu có. | VNet/subnet, public/private endpoint, NSG/route liên quan. | Network path và app test host. |
| Connection strings | JDBC endpoint cho database. | JDBC endpoint cho MI/database. | Không lưu password, chỉ lưu endpoint pattern. |
| Microsoft Entra/admin | Admin/auth model nếu dùng. | Admin/auth model nếu dùng. | Bootstrap admin và app principal plan. |
| Metrics | CPU/storage cơ bản. | CPU/storage/session cơ bản. | Time window và baseline ban đầu. |
| Activity log | Provisioning/deployment events. | Provisioning/deployment events. | Start/end/status/error nếu có. |

Portal evidence mẫu:

```markdown
# Day 0 Azure Portal Evidence

## Resource
- Target type:
- Resource group:
- Region:
- Target name:
- FQDN:
- SKU/tier:

## Networking
- Public access:
- Firewall rule:
- Private endpoint:
- VNet/subnet:
- DNS expectation:

## Security
- Admin/bootstrap principal:
- App principal planned:
- Secret stored outside evidence: yes/no

## Operations
- Activity log status:
- Metrics visible:
- Cleanup owner/date:
```

## Setup tool client để vận hành Azure SQL

Ngày 0 phải chuẩn bị máy vận hành trước khi làm các lab còn lại. Người học cần chứng minh mình có thể vận hành Azure SQL bằng **Azure Portal** và ít nhất một **SQL client**.

| Tool | Vai trò | Khi dùng |
|---|---|---|
| Azure Portal | Tạo/kiểm tra resource, network, metric, restore, Activity log. | Bắt buộc cho Azure DBA junior. |
| SSMS | Query, object explorer, security, script, thao tác quen SQL Server. | Rất nên dùng trên Windows. |
| Azure Data Studio | Query đa nền tảng, notebook, lightweight client. | Tốt cho lab và máy không có SSMS. |
| sqlcmd | Smoke test kết nối nhanh, kiểm tra từ script. | Hữu ích cho checklist/automation. |
| Azure CLI/PowerShell | Kiểm tra resource/firewall và tự động hóa nhẹ. | Hữu ích nhưng không thay SQL validation. |
| Java app/HikariCP | Kiểm tra đường kết nối thật của ứng dụng. | Bắt buộc nếu lab có app. |

### Client setup dossier

```markdown
# DBA Client Setup Dossier

## Workstation
- OS:
- Network path: public internet / VPN / bastion / app subnet:
- Current public IP if using public firewall:
- Can reach Azure Portal: yes/no

## Tools
| Tool | Version | Installed | Notes |
|---|---|---|---|
| SSMS | | | |
| Azure Data Studio | | | |
| sqlcmd | | | |
| Azure CLI | | | |
| PowerShell Az module | | | |

## Connection profiles
| Target | Client | Auth | Result | Evidence |
|---|---|---|---|---|
| Azure SQL Database | SSMS/ADS/sqlcmd | SQL auth/Entra | | |
| Azure SQL Managed Instance | SSMS/ADS/sqlcmd | SQL auth/Entra | | |
| Java app | HikariCP | App user | | |

## Secret handling
- Password/token stored outside evidence:
- Connection string in evidence has secret removed:
```

### Cấu hình kết nối trong SSMS hoặc Azure Data Studio

Track A - Azure SQL Database:

| Trường | Giá trị mẫu |
|---|---|
| Server | `<server>.database.windows.net` |
| Authentication | SQL Login hoặc Microsoft Entra theo lab |
| Database | `SalesAppDB` |
| Encrypt | Bật |
| Trust server certificate | Không bật nếu không có lý do lab rõ |

Track B - Azure SQL Managed Instance:

| Trường | Giá trị mẫu |
|---|---|
| Server | `<managed-instance-fqdn>` |
| Authentication | SQL Login hoặc Microsoft Entra theo lab |
| Database | `SalesAppDB_MI` |
| Network | Máy client phải đi được tới VNet/endpoint của MI |
| Encrypt | Bật |

Smoke query bắt buộc sau khi tạo connection profile:

```sql
SELECT
    @@SERVERNAME AS server_name,
    DB_NAME() AS database_name,
    SUSER_SNAME() AS login_name,
    USER_NAME() AS database_user,
    SYSUTCDATETIME() AS checked_at_utc;
```

### Smoke test bằng sqlcmd

Azure SQL Database:

```powershell
sqlcmd -S "<server>.database.windows.net" -d "SalesAppDB" -U "<user>" -P "<password>" -N -Q "SELECT @@SERVERNAME, DB_NAME(), SUSER_SNAME(), SYSUTCDATETIME();"
```

Managed Instance:

```powershell
sqlcmd -S "<managed-instance-fqdn>" -d "SalesAppDB_MI" -U "<user>" -P "<password>" -N -Q "SELECT @@SERVERNAME, DB_NAME(), SUSER_SNAME(), SYSUTCDATETIME();"
```

Không lưu password thật vào file, wiki, ticket hoặc ảnh chụp.

### Khi nào dùng Azure Portal thay vì SQL client?

| Việc cần làm | Azure Portal | SQL client |
|---|---|---|
| Tạo database/logical server/MI | Chính | Không phù hợp |
| Kiểm tra firewall/private endpoint/VNet | Chính | Chỉ thấy triệu chứng connect fail |
| Lấy connection string pattern | Chính | Không phù hợp |
| Xem CPU/Data IO/Log IO nhanh | Chính | Có thể bổ sung bằng DMV |
| Restore/PITR Azure SQL Database | Chính | SQL client dùng để validate sau restore |
| Chạy query validation | Không phải chính | Chính |
| Kiểm tra permission thực tế | Có thể xem cấu hình | Chính bằng query |
| Điều tra blocking/query chậm | Bổ sung metric | Chính bằng Query Store/DMV |

## Portal operation drill cho Azure DB

Bài này dành cho người mới để hình thành thói quen vận hành: mở đúng resource, đọc đúng trạng thái, biết chỗ kiểm tra trước khi chạy query hoặc đổ lỗi cho database.

### Drill 1 - Xác định resource và endpoint

Trong Portal, mở resource Azure SQL Database hoặc Managed Instance:

- Vào **Overview**.
- Ghi resource name, status, region, resource group.
- Ghi FQDN/endpoint.
- Ghi SKU/tier hoặc compute/storage đang dùng.

Không đạt nếu chỉ ghi "database online" mà không có target type và FQDN.

### Drill 2 - Kiểm tra connection string và network

Track A - Azure SQL Database:

- Mở **Connection strings** để lấy JDBC endpoint pattern.
- Mở **Networking** hoặc **Firewalls and virtual networks** để kiểm tra public access/firewall/private endpoint.
- Nếu dùng public firewall, ghi IP nào được mở và vì sao.
- Nếu dùng private endpoint, ghi connection state và DNS expectation.

Track B - Managed Instance:

- Ghi MI FQDN.
- Kiểm tra VNet/subnet hoặc private/public endpoint path.
- Xác nhận máy client/app host có route tới MI.

### Drill 3 - Chạy smoke query

Ưu tiên dùng SSMS/Azure Data Studio/sqlcmd. Nếu môi trường cho phép dùng Query editor trong Portal, có thể dùng cho smoke query đơn giản:

```sql
SELECT
    @@SERVERNAME AS server_name,
    DB_NAME() AS database_name,
    SUSER_SNAME() AS login_name,
    USER_NAME() AS database_user,
    SYSUTCDATETIME() AS checked_at_utc;
```

Điểm cần nhớ:

- Query editor trong Portal tiện cho kiểm tra nhanh, không thay thế client vận hành đầy đủ.
- Không dùng admin login cho app test.
- Không paste secret vào screenshot hoặc evidence.

### Drill 4 - Đọc metric và Activity log

Trong Portal:

- Mở **Metrics** và chọn window 30-60 phút.
- Ghi CPU, storage, session/worker nếu có.
- Mở **Activity log** để xem operation gần nhất: create, firewall change, private endpoint approval, restore, scale.

Mẫu nộp bài:

```markdown
# Portal Operation Drill - Day 0

## Resource
- Target type:
- Resource name:
- FQDN:
- Region:
- SKU/tier:

## Connectivity
- Connection string pattern checked:
- Network path:
- Firewall/private endpoint finding:
- Client tool used:
- Smoke query result:

## Operations
- Metrics time window:
- CPU/storage/session finding:
- Activity log latest relevant operation:
- Restore/PITR entry point identified:

## Decision
- Ready for day 1:
- Risk:
- Action owner:
```

## Quy ước tên tài nguyên

| Resource | Azure SQL Database | Azure SQL Managed Instance |
|---|---|---|
| Resource group | `rg-dba-lab-sqldb-dev-001` | `rg-dba-lab-sqlmi-dev-001` |
| Region | `southeastasia` hoặc region được phép | Cùng region hoặc region được phép |
| Server/Instance | `sql-dba-lab-<unique>` | `sqlmi-dba-lab-001` |
| Database | `SalesAppDB` | `SalesAppDB_MI` |
| App principal | `app_salesapp` | `app_salesapp_mi` |

Không dùng tên chứa thông tin nhạy cảm, không lưu password vào wiki/evidence.

# Nhánh A - Azure SQL Database

## A1. Tạo bằng Azure Portal

1. Vào Azure Portal.
2. Tạo resource group `rg-dba-lab-sqldb-dev-001`.
3. Vào Azure SQL hoặc SQL databases.
4. Chọn **Create SQL database**.
5. Chọn subscription và resource group.
6. Đặt database name `SalesAppDB`.
7. Tạo logical server mới, ví dụ `sql-dba-lab-<unique>`.
8. Chọn region.
9. Chọn authentication/admin.
10. Chọn compute + storage nhỏ phù hợp lab.
11. Review networking.
12. Review + Create.

Decision cần ghi:

| Quyết định | Ghi chú |
|---|---|
| Tier | Vì sao chọn tier này, có kiểm soát chi phí không. |
| Backup redundancy | Theo lab hay policy tổ chức. |
| Admin | Chỉ dùng bootstrap, không dùng cho app. |
| Tags | Owner, Environment, ExpiryDate nếu có. |

## A2. Tạo bằng Azure CLI

```powershell
az login
az account show
```

```powershell
$rg = "rg-dba-lab-sqldb-dev-001"
$location = "southeastasia"
$server = "sql-dba-lab-001-unique"
$db = "SalesAppDB"
$admin = "sqladmin_labs"
```

```powershell
az group create `
  --name $rg `
  --location $location

az sql server create `
  --name $server `
  --resource-group $rg `
  --location $location `
  --admin-user $admin `
  --admin-password "<nhap-password-an-toan-khong-luu-vao-wiki>"

az sql db create `
  --resource-group $rg `
  --server $server `
  --name $db `
  --service-objective S0
```

## A3. Network access cho Azure SQL Database

Có hai hướng:

| Hướng | Khi dùng | Yêu cầu |
|---|---|---|
| Public endpoint + firewall IP cá nhân | Lab nhanh, học cơ bản. | Chỉ mở IP hiện tại, không mở rộng. |
| Private endpoint | Gần production hơn. | Cần VNet, private DNS, kiểm tra public access. |

Firewall rule cho lab:

```powershell
$myIp = "<dia-chi-ip-public-cua-ban>"

az sql server firewall-rule create `
  --resource-group $rg `
  --server $server `
  --name "AllowMyCurrentIpForLab" `
  --start-ip-address $myIp `
  --end-ip-address $myIp
```

Điểm cần hiểu:

- Azure SQL logical server có endpoint dạng `<server>.database.windows.net`.
- Mặc định server chặn kết nối nếu chưa có network rule phù hợp.
- Private endpoint không tự động nghĩa là public path đã bị chặn; nếu muốn chặn public, phải cấu hình rõ.
- Không bật "Allow Azure services" nếu không có lý do.

## A4. Tạo app user trong Azure SQL Database

Kết nối bằng admin chỉ để bootstrap:

```sql
CREATE USER app_salesapp WITH PASSWORD = '<khong-luu-password-vao-wiki>';
GO

CREATE ROLE app_executor;
GO

GRANT EXECUTE ON SCHEMA::dbo TO app_executor;
GO

ALTER ROLE app_executor ADD MEMBER app_salesapp;
GO
```

Nếu lab dùng table trực tiếp:

```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA::dbo TO app_executor;
GO
```

Đáp án đạt:

- App user riêng.
- Không dùng server admin cho app.
- Không cấp `db_owner` nếu không cần.
- Password không lưu trong evidence.

## A5. Smoke test Azure SQL Database

```sql
SELECT
    DB_NAME() AS database_name,
    SUSER_SNAME() AS login_name,
    USER_NAME() AS database_user,
    SYSUTCDATETIME() AS checked_at_utc;
```

```sql
CREATE TABLE dbo.CloudProvisioningSmokeTest
(
    SmokeTestId INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
    CreatedAtUtc DATETIME2(3) NOT NULL DEFAULT SYSUTCDATETIME(),
    Note NVARCHAR(200) NOT NULL
);

INSERT dbo.CloudProvisioningSmokeTest (Note)
VALUES (N'Azure SQL Database smoke test');

SELECT *
FROM dbo.CloudProvisioningSmokeTest;
```

## A6. Connection string Java cho Azure SQL Database

```properties
spring.datasource.url=jdbc:sqlserver://sql-dba-lab-001-unique.database.windows.net:1433;databaseName=SalesAppDB;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30
spring.datasource.username=app_salesapp
spring.datasource.password=<lay-tu-secret-store-hoac-env-var>
spring.datasource.hikari.maximum-pool-size=5
spring.datasource.hikari.minimum-idle=1
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
```

# Nhánh B - Azure SQL Managed Instance

## B1. Điểm khác biệt cần nhớ

Azure SQL Managed Instance là instance-level PaaS. MI được triển khai trong VNet/subnet phù hợp và thường giống SQL Server hơn về trải nghiệm instance. Tạo MI thường lâu hơn và tốn chi phí hơn Azure SQL Database, nên phải có owner và cleanup date.

Theo Microsoft Learn, để tạo SQL Managed Instance cần subscription, quyền phù hợp như SQL Managed Instance Contributor hoặc quyền tương đương, và cấu hình network/subnet đúng.

## B2. Tạo bằng Azure Portal

1. Tạo resource group `rg-dba-lab-sqlmi-dev-001`.
2. Tạo hoặc chọn VNet, ví dụ `vnet-dba-lab-mi-001`.
3. Tạo subnet dedicated cho MI, ví dụ `snet-sqlmi-001`.
4. Đảm bảo subnet được cấu hình/delegated cho `Microsoft.Sql/managedInstances`.
5. Vào **Azure SQL Managed Instance**.
6. Chọn **Create**.
7. Chọn subscription, resource group, managed instance name.
8. Chọn region.
9. Chọn compute + storage nhỏ nhất phù hợp lab nếu được phép.
10. Cấu hình authentication/admin.
11. Chọn VNet/subnet.
12. Cấu hình public endpoint nếu thật sự cần; ưu tiên private/VNet path trong lab production-like.
13. Review + Create.

Evidence cần lưu:

| Evidence | Nội dung |
|---|---|
| MI name | `sqlmi-dba-lab-001` |
| Resource group | `rg-dba-lab-sqlmi-dev-001` |
| Region | Region đã chọn |
| VNet/subnet | Tên và address range |
| Subnet delegation | Có |
| Compute/storage | Giá trị đã chọn |
| Public/private endpoint | Trạng thái |
| Owner/ExpiryDate | Người chịu trách nhiệm và ngày cleanup |

Nếu không được phép tạo MI thật:

```text
Không fake kết quả. Ghi rõ blocker: thiếu quyền, quota hoặc cost approval.
Nộp provisioning design dossier đầy đủ và yêu cầu sandbox/approval.
```

## B3. Tạo bằng Azure CLI

Managed Instance CLI cần network. Skeleton dưới đây dùng để học thành phần; khi chạy thật phải kiểm tra quota, region và policy tổ chức.

```powershell
$rg = "rg-dba-lab-sqlmi-dev-001"
$location = "southeastasia"
$vnet = "vnet-dba-lab-mi-001"
$subnet = "snet-sqlmi-001"
$mi = "sqlmi-dba-lab-001"
$admin = "sqladmin_labs"
```

```powershell
az group create `
  --name $rg `
  --location $location

az network vnet create `
  --resource-group $rg `
  --name $vnet `
  --location $location `
  --address-prefixes 10.20.0.0/16

az network vnet subnet create `
  --resource-group $rg `
  --vnet-name $vnet `
  --name $subnet `
  --address-prefixes 10.20.1.0/24 `
  --delegations Microsoft.Sql/managedInstances
```

Tạo MI:

```powershell
$subnetId = "/subscriptions/<subscription-id>/resourceGroups/$rg/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/$subnet"

az sql mi create `
  --resource-group $rg `
  --name $mi `
  --location $location `
  --subnet $subnetId `
  --admin-user $admin `
  --admin-password "<nhap-password-an-toan-khong-luu-vao-wiki>" `
  --license-type BasePrice `
  --storage 32GB `
  --capacity 4 `
  --edition GeneralPurpose `
  --family Gen5
```

Lưu ý:

- Tham số compute/storage/license có thể khác theo region, quota và thời điểm.
- Không chạy lệnh tạo MI nếu chưa kiểm soát chi phí.
- Nếu fail do quota/subnet/policy, ghi thành issue vận hành.

## B4. Tạo database trong Managed Instance

Sau khi kết nối tới MI:

```sql
CREATE DATABASE SalesAppDB_MI;
GO

SELECT
    name,
    state_desc,
    compatibility_level,
    create_date
FROM sys.databases
WHERE name = 'SalesAppDB_MI';
```

## B5. Tạo app login/user trong Managed Instance

MI gần SQL Server hơn, nên dùng login ở instance level rồi map user trong database:

```sql
USE master;
GO

CREATE LOGIN app_salesapp_mi
WITH PASSWORD = '<khong-luu-password-vao-wiki>';
GO

USE SalesAppDB_MI;
GO

CREATE USER app_salesapp_mi FOR LOGIN app_salesapp_mi;
GO

CREATE ROLE app_executor;
GO

GRANT EXECUTE ON SCHEMA::dbo TO app_executor;
GO

ALTER ROLE app_executor ADD MEMBER app_salesapp_mi;
GO
```

Đáp án đạt:

- Login tồn tại ở instance level.
- User tồn tại trong database.
- App không dùng admin.
- Không cấp `sysadmin` hoặc `db_owner` tùy tiện.

## B6. Kết nối tới Managed Instance

Managed Instance có kiến trúc kết nối khác Azure SQL Database. Theo Microsoft Learn, MI nằm trong VNet và có endpoint VNet-local; private endpoint là tùy chọn. Khi dùng private endpoint, phải kết nối bằng domain name, không dùng IP.

Checklist:

| Kiểm tra | Ý nghĩa |
|---|---|
| FQDN | Dùng domain name của MI, không dùng IP. |
| Network path | App/VM phải ở VNet, peered VNet, VPN/ExpressRoute hoặc private endpoint path. |
| DNS | Private DNS/domain resolution đúng. |
| NSG/route | Không chặn traffic tới MI. |
| Public endpoint | Nếu bật, dùng đúng FQDN/port và chỉ mở phạm vi cần thiết. |
| Login/user | Login được map đúng user trong database. |

Smoke test:

```sql
SELECT
    @@SERVERNAME AS server_name,
    DB_NAME() AS database_name,
    SUSER_SNAME() AS login_name,
    USER_NAME() AS database_user,
    SYSUTCDATETIME() AS checked_at_utc;
```

## B7. Connection string Java cho MI

Ví dụ private/VNet path:

```properties
spring.datasource.url=jdbc:sqlserver://<mi-fqdn>:1433;databaseName=SalesAppDB_MI;encrypt=true;trustServerCertificate=false;loginTimeout=30
spring.datasource.username=app_salesapp_mi
spring.datasource.password=<lay-tu-secret-store-hoac-env-var>
spring.datasource.hikari.maximum-pool-size=5
spring.datasource.hikari.minimum-idle=1
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
```

Nếu dùng public endpoint của MI, phải dùng đúng FQDN/port theo cấu hình MI và mở network đúng phạm vi lab. Không dùng IP thay domain name.

## B8. Monitoring tối thiểu cho MI

| Khu vực | Cần xem |
|---|---|
| Overview | State, FQDN, resource group, region. |
| Compute/storage | vCore, storage used, tier. |
| Networking | VNet/subnet, public endpoint, private endpoint nếu có. |
| Metrics | CPU, storage, IO, sessions/workers nếu có. |
| SQL Agent | Job nếu dùng migration/runbook. |
| Query Store | Top queries sau khi có workload. |

# So sánh validation hai loại

| Validation | Azure SQL Database | Azure SQL Managed Instance |
|---|---|---|
| Database online | Check database overview/T-SQL. | Check MI state + database state. |
| App identity | Contained user hoặc auth theo thiết kế. | Login ở instance + user trong database. |
| Network | Firewall/private endpoint logical server. | VNet/subnet/private/public endpoint MI. |
| Smoke query | Query editor/SSMS/app. | SSMS/app từ network có route tới MI. |
| Monitoring | Database metrics + Query Store. | Instance/database metrics + Query Store + SQL Agent nếu dùng. |
| Cleanup | Xóa DB/server/RG. | Xóa MI/RG, chú ý thời gian và chi phí. |

## Dossier mẫu

```markdown
# Azure SQL Provisioning Dossier

## Scope
- Ticket: LAB-DBA-D0
- Subscription: <masked>
- Region: southeastasia
- Owner: DBA trainee

## Azure SQL Database
- Resource group: rg-dba-lab-sqldb-dev-001
- Server: sql-dba-lab-001-unique
- Database: SalesAppDB
- Network: Public endpoint + single IP firewall
- App user: app_salesapp
- Smoke test: Pass

## Azure SQL Managed Instance
- Resource group: rg-dba-lab-sqlmi-dev-001
- Managed instance: sqlmi-dba-lab-001
- Database: SalesAppDB_MI
- VNet/subnet: vnet-dba-lab-mi-001 / snet-sqlmi-001
- App login/user: app_salesapp_mi
- Smoke test: Pass

## Risks and actions
| Risk | Action | Owner |
|---|---|---|
| SQL Database public endpoint used for lab | Remove firewall rule or switch to private endpoint | DBA trainee |
| Managed Instance cost higher | Add ExpiryDate tag and cleanup date | DBA trainee |
| Manual password handling | Move to secret store/env var | App team |

## Cleanup
- Delete SQL DB lab resource group if no longer needed.
- Delete MI lab resource group if no longer needed.
- Review date:
```

## Tình huống lỗi và lời giải

| Lỗi | Cách phân tích |
|---|---|
| Cannot connect to Azure SQL Database from local | Kiểm tra server-level firewall rule cho IP hiện tại. |
| Azure SQL Database login failed | Kiểm tra auth, username/password, contained user. |
| Azure SQL Database permission denied | Login được nhưng thiếu quyền database. |
| Cannot reach Managed Instance | Kiểm tra VNet/subnet, route, DNS, private/public endpoint. |
| MI private endpoint fail | Kiểm tra private DNS, domain name, approval private endpoint. |
| MI login works but cannot open database | Kiểm tra login-user mapping và database name. |
| TLS/certificate error | Kiểm tra JDBC driver và connection string. |
| Hikari pending tăng | Kiểm tra network latency, session/resource limit, query wait. |

## Dấu hiệu làm sai bài

- Nói "Azure SQL" chung chung mà không phân biệt Database và Managed Instance.
- Dùng admin login cho app.
- Mở firewall rule quá rộng.
- Tạo MI nhưng không hiểu VNet/subnet/DNS.
- Không ghi tier/cost.
- Không test bằng app user.
- Không có cleanup plan.
- Lưu password trong evidence.

## Chấm điểm gợi ý

| Hạng mục | Điểm tối đa |
|---|---:|
| Phân biệt đúng hai loại Azure SQL | 10 |
| Azure SQL Database tạo đúng và cost-aware | 15 |
| Azure SQL Managed Instance tạo đúng hoặc có design dossier đầy đủ | 20 |
| Network/firewall/VNet/private access có kiểm soát | 20 |
| App user/security cho cả hai loại | 15 |
| Smoke test và monitoring evidence | 10 |
| Cleanup plan | 10 |
