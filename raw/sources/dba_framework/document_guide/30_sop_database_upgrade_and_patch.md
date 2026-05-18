# SOP Database Upgrade and Patch

## Thông tin tài liệu

| Trường | Giá trị |
|--------|--------|
| Mã tài liệu | DBA-SOP-030 |
| Loại tài liệu | SOP |
| Mức ưu tiên triển khai | 4 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Service Owner, Infra Lead |
| Approver | DBA Lead |
| Trạng thái | Draft |
| Phiên bản | 0.2 |
| Ngày tạo | 2026-05-18 |
| Ngày review gần nhất | 2026-05-18 |
| Ngày review tiếp theo | 2026-11-18 |
| Phạm vi áp dụng | SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB |
| Chu kỳ review | 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi policy |

### Lịch sử thay đổi

| Phiên bản | Ngày | Người thay đổi | Mô tả |
|-----------|------|----------------|-------|
| 0.1 | 2026-05-18 | DBA Team | Bản draft đầu tiên |
| 0.2 | 2026-05-18 | DBA Team | Chuẩn hóa metadata, cross-reference |

## 1. Mục đích

Chuẩn hóa nâng cấp và vá lỗi database để giảm rủi ro downtime, lỗi tương thích, lỗi hiệu năng và đảm bảo có rollback hoặc recovery plan rõ ràng.

## 2. Phạm vi

Áp dụng cho patch engine, minor upgrade, major upgrade, extension upgrade, compatibility level change, cloud database maintenance, driver compatibility review và các thay đổi liên quan database platform.

## 3. Nguyên tắc áp dụng

Bộ SOP này thuộc DBA Operations Framework. Mỗi SOP được thiết kế để dùng trong vận hành thực tế, có thể gắn với ticket ITSM, ServiceNow, Jira hoặc công cụ quản trị nội bộ. Khi áp dụng cho từng DBMS cụ thể, DBA cần dùng thêm phụ lục kỹ thuật tương ứng như SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle hoặc Z DB.


| Thành phần | Nội dung |
|---|---|
| Ticket bắt buộc | Có |
| Approval bắt buộc | Tùy mức rủi ro và môi trường |
| Evidence bắt buộc | Có |
| Áp dụng production | Có, nhưng phải tuân thủ Change Management |
| Áp dụng non production | Có, có thể rút gọn approval theo quy định nội bộ |
| Tài liệu liên quan | DBA Service Catalog, DBA Operating Model, DBA RACI Matrix, DBA Document Governance Standard |

## 4. Khi nào sử dụng SOP này

1. Có bản vá bảo mật.
2. Có yêu cầu nâng cấp version.
3. Có yêu cầu từ vendor hoặc platform team.
4. Có lỗi cần patch để khắc phục.
5. Có kế hoạch modernization hoặc migration.
6. Cloud provider thông báo maintenance ảnh hưởng database.

## 5. Điều kiện tiên quyết

1. Có RFC được phê duyệt.
2. Có đánh giá compatibility và impact.
3. Có backup hợp lệ trước nâng cấp.
4. Có môi trường test hoặc staging để diễn tập.
5. Có kế hoạch rollback hoặc restore.
6. Có maintenance window và truyền thông stakeholder.
7. Có checklist pre upgrade và post upgrade.

## 6. Vai trò và trách nhiệm

| Vai trò | Trách nhiệm |
|---|---|
| DBA | Thực hiện kiểm tra, thao tác kỹ thuật, ghi nhận evidence và cập nhật ticket. |
| Database Lead | Review quyết định kỹ thuật có rủi ro cao và hỗ trợ escalation. |
| Service Owner | Phê duyệt tác động nghiệp vụ, downtime, mất dữ liệu chấp nhận được hoặc kết quả bàn giao. |
| Application Team | Xác nhận ảnh hưởng ứng dụng, hỗ trợ validation và xử lý dependency phía ứng dụng. |
| Infra hoặc Cloud Team | Hỗ trợ hạ tầng, network, storage, compute, load balancer, DNS hoặc resource cloud nếu cần. |
| Security Team | Review yêu cầu liên quan quyền cao, dữ liệu nhạy cảm, audit, mã hóa hoặc compliance. |

## 7. Quy trình thực hiện chi tiết

### 7.1. Lập kế hoạch nâng cấp

1. Xác định version hiện tại, version đích, lý do nâng cấp và phạm vi database affected.
2. Đọc release note, known issue, breaking change và yêu cầu license nếu có.
3. Xác định downtime, rủi ro, dependency ứng dụng và driver.

### 7.2. Kiểm tra trước nâng cấp

1. Kiểm tra health check database.
2. Kiểm tra backup gần nhất và thực hiện backup trước nâng cấp nếu policy yêu cầu.
3. Kiểm tra replication, HA, disk, log space và capacity.
4. Kiểm tra query baseline, top query và job quan trọng.
5. Chạy compatibility check hoặc upgrade advisor nếu DBMS hỗ trợ.

### 7.3. Diễn tập trên môi trường non production

1. Thực hiện upgrade trên staging hoặc clone gần giống production.
2. Chạy smoke test, regression test và performance sanity check.
3. Ghi nhận thời gian upgrade, lỗi phát sinh và điều chỉnh runbook.

### 7.4. Thực hiện nâng cấp production

1. Thông báo bắt đầu maintenance.
2. Dừng hoặc kiểm soát workload theo kế hoạch.
3. Chạy runbook upgrade hoặc patch.
4. Theo dõi log, tiến trình và cảnh báo.
5. Không mở rộng scope ngoài RFC đã phê duyệt.

### 7.5. Kiểm tra sau nâng cấp

1. Xác nhận database online.
2. Kiểm tra version, patch level và configuration.
3. Kiểm tra ứng dụng kết nối được.
4. Kiểm tra job, backup, replication, monitoring và alert.
5. Cập nhật statistics, rebuild metadata hoặc chạy post task theo từng DBMS nếu cần.

### 7.6. Quyết định rollback nếu thất bại

1. Nếu post check thất bại nghiêm trọng, kích hoạt rollback hoặc restore theo kế hoạch.
2. Ghi rõ thời điểm quyết định rollback và người approve.
3. Không tiếp tục thử nghiệm trực tiếp trên production nếu vượt ngưỡng rủi ro.

### 7.7. Hoàn tất và cập nhật tài liệu

1. Thông báo hoàn tất maintenance.
2. Cập nhật CMDB, inventory, version matrix và SOP nếu cần.
3. Lưu evidence, log và kết quả post check vào RFC.

## 8. Evidence bắt buộc

1. RFC approval.
2. Release note hoặc risk assessment.
3. Pre check report.
4. Backup evidence trước nâng cấp.
5. Upgrade log hoặc patch log.
6. Post check report.
7. Kết quả ứng dụng xác nhận.
8. Rollback evidence nếu có.

## 9. Rollback hoặc phương án khôi phục

Rollback phải được xác định trước khi nâng cấp. Với một số DBMS, downgrade trực tiếp có thể không được hỗ trợ, khi đó rollback thực tế là restore từ backup hoặc failover về node chưa nâng cấp. Không thực hiện upgrade nếu rollback path chưa được chấp nhận bởi service owner.

## 10. Điều kiện escalation

1. Patch liên quan lỗ hổng bảo mật nghiêm trọng.
2. Nâng cấp major version có breaking change.
3. Post check thất bại hoặc ứng dụng không kết nối được.
4. Replication hoặc HA lỗi sau nâng cấp.
5. Rollback có nguy cơ mất dữ liệu hoặc vượt maintenance window.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Kiểm tra compatibility level, cumulative update, Always On rolling upgrade, SQL Agent job và Query Store regression. |
| PostgreSQL | Kiểm tra major upgrade, extension compatibility, pg upgrade, logical replication option và analyze sau nâng cấp. |
| MySQL MariaDB | Kiểm tra version compatibility, replication compatibility, SQL mode, optimizer change và backup trước nâng cấp. |
| Oracle | Kiểm tra OPatch, datapatch, invalid objects, statistics, listener và Data Guard compatibility. |
| Azure SQL | Kiểm tra maintenance event, compatibility level, automatic tuning và feature behavior thay đổi. |
| Z DB | Tuân theo quy trình patch, maintenance và rollback được nền tảng Z DB hỗ trợ. |

## 12. Definition of Done

1. Database đã nâng cấp hoặc patch đúng version mục tiêu.
2. Ứng dụng và job quan trọng đã hoạt động bình thường.
3. Backup, monitoring, replication và alert đã được xác nhận.
4. Evidence đã lưu vào RFC.
5. CMDB hoặc inventory đã cập nhật version mới.

## 13. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-POL-004 | Database Change Management Policy | Policy change |
| DBA-POL-005 | Backup and Restore Policy | Backup trước upgrade |
| DBA-SOP-028 | SOP HA Failover and Switchover | Rolling upgrade |
| DBA-TMP-045 | Template Request for Change | Template RFC |
| DBA-TMP-048 | Template Operation Evidence | Template evidence |

## 14. Checklist rút gọn

1. Ticket hoặc trigger đã rõ.
2. Phạm vi database và môi trường đã xác định.
3. Approval đã có nếu tác động production hoặc dữ liệu nhạy cảm.
4. Rủi ro đã được đánh giá.
5. Các bước kỹ thuật đã thực hiện theo SOP hoặc runbook.
6. Kết quả đã được kiểm tra.
7. Evidence đã được lưu.
8. Ticket đã được cập nhật hoặc đóng đúng trạng thái.
