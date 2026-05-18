# DBA RACI Matrix

## Thông tin tài liệu

| Trường | Giá trị |
|--------|---------|
| Mã tài liệu | DBA-OM-003 |
| Loại tài liệu | Operating Model |
| Mức ưu tiên triển khai | 3 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Infra Lead, Security Lead |
| Approver | Service Owner |
| Trạng thái | Draft |
| Phiên bản | 0.2 |
| Ngày tạo | 2026-05-18 |
| Ngày review gần nhất | 2026-05-18 |
| Ngày review tiếp theo | 2026-11-18 |
| Phạm vi áp dụng | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật |

### Lịch sử thay đổi

| Phiên bản | Ngày | Người thay đổi | Mô tả |
|-----------|------|----------------|-------|
| 0.1 | 2026-05-18 | DBA Team | Bản draft đầu tiên |
| 0.2 | 2026-05-18 | DBA Team | Chuyển sang format bảng matrix, bổ sung activity, chuẩn hóa metadata |

## 1. Mục đích

Tài liệu này làm rõ trách nhiệm giữa DBA team và các bên liên quan trong những hoạt động vận hành database. Mục tiêu là tránh tình trạng không rõ ai thực hiện, ai phê duyệt, ai chịu trách nhiệm cuối cùng và ai cần được thông báo.

## 2. Quy ước RACI

| Ký hiệu | Vai trò | Mô tả |
|----------|---------|-------|
| R | Responsible | Người thực hiện chính |
| A | Accountable | Người chịu trách nhiệm cuối cùng, phê duyệt kết quả |
| C | Consulted | Bên cần tham vấn trước hoặc trong khi thực hiện |
| I | Informed | Bên cần được thông báo sau khi hoàn thành |

## 3. Vai trò sử dụng trong ma trận

| Viết tắt | Vai trò | Mô tả |
|----------|---------|-------|
| DBA | Database Administrator | Người thực hiện tác vụ database |
| DBA-L | DBA Lead | Người phụ trách kỹ thuật DBA |
| APP | Application Team | Đội phát triển hoặc vận hành ứng dụng |
| INFRA | Infrastructure Team | Đội hạ tầng, OS, storage, network, cloud |
| SEC | Security Team | Đội bảo mật và tuân thủ |
| SO | Service Owner | Chủ sở hữu dịch vụ nghiệp vụ |
| CAB | Change Advisory Board | Hội đồng tư vấn thay đổi |
| SD | Service Desk | Đội tiếp nhận và điều phối ticket |
| VND | Vendor / Platform Team | Nhà cung cấp hoặc đội nền tảng |

## 4. Ma trận RACI chính

### 4.1. Provisioning và Access

| Hoạt động | DBA | DBA-L | APP | INFRA | SEC | SO | CAB | SD |
|-----------|-----|-------|-----|-------|-----|-----|-----|-----|
| Tạo database mới | R | C | C | C | C¹ | A | I² | I |
| Tạo schema | R | C | C | | | A | | I |
| Tạo user hoặc role | R | C³ | C | | C³ | A | | I |
| Cấp quyền database | R | C³ | C | | A⁴ | A | | I |
| Thu hồi quyền | R | | C⁵ | | A | C | | I |
| Setup monitoring cho DB mới | R | C | C | C | | A | | I |

> ¹ Khi dữ liệu nhạy cảm (P2, P3). ² Khi production hoặc change lớn. ³ Khi quyền cao. ⁴ Khi quyền production hoặc dữ liệu nhạy cảm. ⁵ Khi là service account.

### 4.2. Backup và Restore

| Hoạt động | DBA | DBA-L | APP | INFRA | SEC | SO | CAB | SD |
|-----------|-----|-------|-----|-------|-----|-----|-----|-----|
| Backup định kỳ | R | A | | C¹ | C² | I | | |
| Backup ad hoc | R | A | C | C¹ | | I | | I |
| Restore dữ liệu | R | A³ | C | C¹ | C⁴ | A³ | C⁵ | I |
| Point in time recovery | R | A | C | | C⁴ | A | C⁶ | I |
| Restore drill | R | A | C | C | | I | | I |
| Xóa backup trước retention | R | A | | | C | A | | |

> ¹ Khi liên quan storage hoặc backup platform. ² Khi liên quan encryption hoặc key. ³ Khi production hoặc restore rủi ro cao. ⁴ Khi dữ liệu nhạy cảm. ⁵ Khi restore có downtime hoặc rủi ro lớn. ⁶ Khi production.

### 4.3. HA và DR

| Hoạt động | DBA | DBA-L | APP | INFRA | SEC | SO | CAB | VND |
|-----------|-----|-------|-----|-------|-----|-----|-----|-----|
| Failover khẩn cấp | R | A | C | C | I | A | I | C¹ |
| Switchover có kế hoạch | R | A | C | C | | A | A | |
| DR drill | R | A | C | C | C² | A | I | C¹ |
| Bổ sung hoặc thay replica | R | A | C | C | | A | I | C¹ |
| Kiểm tra replication lag | R | C | I | C | | I | | |
| Rebuild replica | R | A | I | C | | I | | C¹ |

> ¹ Khi là managed platform. ² Khi có dữ liệu nhạy cảm hoặc thay đổi quyền.

### 4.4. Change và Release

| Hoạt động | DBA | DBA-L | APP | INFRA | SEC | SO | CAB | SD |
|-----------|-----|-------|-----|-------|-----|-----|-----|-----|
| Review schema change | R | C¹ | R | | C² | A | A³ | |
| Triển khai schema change | R⁴ | C | R⁵ | | | A | A³ | I |
| Data migration | R | A | R | C | C² | A | A³ | I |
| Database upgrade hoặc patch | R | A | C | C | C | A | A | I |
| Thay đổi database config | R | A | C | C | C | A | C³ | I |

> ¹ Khi rủi ro cao. ² Khi dữ liệu nhạy cảm. ³ Khi production change. ⁴ Nếu DBA thực thi script. ⁵ Nếu deployment pipeline thuộc app team.

### 4.5. Performance và Capacity

| Hoạt động | DBA | DBA-L | APP | INFRA | SEC | SO | CAB | SD |
|-----------|-----|-------|-----|-------|-----|-----|-----|-----|
| Điều tra query chậm | R | C¹ | R | C² | | I | | |
| Xử lý blocking hoặc deadlock | R | A³ | C | | | C⁴ | I⁵ | |
| Đề xuất index | R | C | C | | | I | | |
| Capacity planning | R | A | C | C | | A | | |
| Scale up hoặc scale out | R | A | C | R | | A | A | I |

> ¹ Khi ảnh hưởng production nghiêm trọng. ² Khi liên quan tài nguyên hệ thống. ³ Khi cần kill session production. ⁴ Khi có ảnh hưởng nghiệp vụ. ⁵ Khi phát sinh emergency change.

### 4.6. Incident và Compliance

| Hoạt động | DBA | DBA-L | APP | INFRA | SEC | SO | CAB | SD |
|-----------|-----|-------|-----|-------|-----|-----|-----|-----|
| RCA hoặc PIR sau sự cố | R | C | R¹ | R¹ | R² | A | | I |
| Review quyền định kỳ | R | C | C³ | | A | A | | I |
| Xuất audit log | R | C | | | A | I | | |
| Decommission database | R | C | C | C | C⁴ | A | A⁵ | I |
| Data masking cho non-prod | R | C | C | | A | A | | |

> ¹ Với phần thuộc phạm vi. ² Khi có yếu tố bảo mật. ³ Với service account. ⁴ Khi có dữ liệu nhạy cảm. ⁵ Khi production.

## 5. Quy tắc áp dụng

1. Mỗi ticket production phải xác định ít nhất một A.
2. Không để một hoạt động rủi ro cao chỉ có R mà không có A.
3. Người thực hiện (R) và người phê duyệt (A) không nên là cùng một người với change rủi ro cao.
4. RACI phải được cập nhật khi tổ chức thay đổi vai trò hoặc quy trình.
5. Nếu có xung đột trách nhiệm, DBA Lead và Service Owner phải thống nhất trước khi thực hiện.
6. Khi một hoạt động có nhiều A, phải xác định rõ A nào quyết định cuối cùng trong trường hợp bất đồng.

## 6. Evidence liên quan RACI

1. Approval record trong ticket.
2. Ticket history ghi nhận người thực hiện.
3. Comment xác nhận của application owner.
4. CAB approval nếu có.
5. Security approval nếu có.
6. Operation evidence sau thực hiện.

## 7. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-OF-001 | DBA Operations Framework Overview | Tổng quan framework |
| DBA-OM-001 | DBA Service Catalog | Danh mục dịch vụ DBA |
| DBA-OM-002 | DBA Operating Model | Mô hình vận hành và escalation |
| DBA-POL-004 | Database Change Management Policy | Quy trình change |
| DBA-POL-005 | Backup and Restore Policy | Quy trình backup restore |
| DBA-POL-007 | Database HA and DR Policy | Quy trình HA DR |
