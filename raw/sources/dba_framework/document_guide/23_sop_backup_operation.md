# SOP Backup Operation

## Thông tin tài liệu

| Trường | Giá trị |
|--------|--------|
| Mã tài liệu | DBA-SOP-023 |
| Loại tài liệu | SOP |
| Mức ưu tiên triển khai | 1 |
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

Chuẩn hóa vận hành backup để đảm bảo database có bản sao lưu hợp lệ, đáp ứng RPO, có thể phục hồi và được kiểm soát bằng evidence.

## 2. Phạm vi

Áp dụng cho kiểm tra backup định kỳ, backup ad hoc, xử lý backup failed, kiểm tra backup chain, kiểm tra dung lượng backup và xác nhận backup hợp lệ trên các DBMS trong phạm vi quản lý.

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

1. Backup job định kỳ cần kiểm tra.
2. Backup job failed hoặc overdue.
3. Trước khi thực hiện thay đổi production có rủi ro.
4. Trước khi upgrade, patch, migration, schema change lớn hoặc data change lớn.
5. Có yêu cầu backup ad hoc từ service owner hoặc change ticket.

## 5. Điều kiện tiên quyết

1. Có Backup and Restore Policy đã được phê duyệt.
2. Có thông tin RPO, retention và backup schedule của database.
3. Có quyền kiểm tra backup job, backup repository và backup log.
4. Có đủ dung lượng backup storage.
5. Với backup ad hoc production, có approval hoặc change ticket hợp lệ.

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

### 7.1. Xác định yêu cầu backup

1. Xác định backup định kỳ hay backup ad hoc.
2. Xác định database, môi trường, thời điểm backup, loại backup và retention.
3. Xác định backup có cần mã hóa hoặc lưu offsite hay không.

### 7.2. Kiểm tra trạng thái backup hiện tại

1. Kiểm tra backup gần nhất có thành công không.
2. Kiểm tra backup age so với RPO.
3. Kiểm tra backup duration, backup size và lỗi gần nhất.
4. Kiểm tra backup chain đối với DBMS dùng log backup, WAL hoặc binlog.

### 7.3. Thực hiện backup ad hoc nếu được yêu cầu

1. Xác nhận approval và thời điểm thực hiện.
2. Chạy script hoặc job backup đã chuẩn hóa.
3. Không dùng lệnh thủ công không được kiểm soát nếu đã có runbook.
4. Ghi lại thời gian bắt đầu và kết thúc.

### 7.4. Xác minh backup file

1. Kiểm tra backup file tồn tại ở đúng vị trí.
2. Kiểm tra kích thước file có hợp lý không.
3. Kiểm tra checksum, verify only hoặc công cụ xác thực tương ứng nếu DBMS hỗ trợ.
4. Kiểm tra quyền truy cập backup file và mã hóa nếu có yêu cầu.

### 7.5. Xử lý backup failed

1. Đọc log lỗi để xác định nguyên nhân ban đầu.
2. Kiểm tra dung lượng, quyền ghi, network, lock, job scheduler và lỗi engine.
3. Thực hiện retry theo quy trình nếu rủi ro thấp.
4. Nếu backup không thể hoàn thành trong RPO, escalate ngay theo severity.

### 7.6. Cập nhật evidence

1. Ghi nhận database, backup type, thời gian, vị trí lưu, kết quả verify, người thực hiện.
2. Gắn log hoặc output script vào ticket.
3. Cập nhật dashboard hoặc daily report nếu cần.

## 8. Evidence bắt buộc

1. Backup job history.
2. Backup file path hoặc backup ID.
3. Thời gian bắt đầu và kết thúc backup.
4. Kết quả verify backup.
5. Log lỗi nếu backup failed.
6. Ticket approval nếu là backup ad hoc production.

## 9. Rollback hoặc phương án khôi phục

Backup operation thường không cần rollback. Nếu backup ad hoc tạo ra file không cần thiết hoặc sai vị trí, xử lý theo retention và data security policy, không xóa tùy tiện khi chưa xác nhận backup chain và yêu cầu audit.

## 10. Điều kiện escalation

1. Backup failed làm vi phạm RPO.
2. Backup storage gần đầy hoặc không ghi được.
3. Backup chain bị đứt.
4. Backup file không verify được.
5. Backup chứa dữ liệu nhạy cảm nhưng chưa mã hóa hoặc lưu sai vị trí.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Kiểm tra full, differential, log backup, msdb backup history, SQL Agent job và checksum nếu áp dụng. |
| PostgreSQL | Kiểm tra base backup, WAL archive, archive command, replication slot và khả năng PITR. |
| MySQL MariaDB | Kiểm tra physical backup, logical backup, binlog, GTID và backup consistency. |
| Oracle | Kiểm tra RMAN backup, archive log backup, control file backup và catalog nếu có. |
| Azure SQL | Xác nhận automatic backup, PITR window, long term retention và export nếu có yêu cầu riêng. |
| Z DB | Theo dõi backup mechanism do nền tảng Z DB cung cấp, kèm trạng thái job và khả năng restore. |

## 12. Definition of Done

1. Backup gần nhất đáp ứng RPO.
2. Backup file hoặc backup record tồn tại hợp lệ.
3. Backup đã được verify theo khả năng của DBMS.
4. Không có backup failed critical chưa xử lý.
5. Evidence đã được lưu vào ticket hoặc repository.

## 13. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-POL-005 | Backup and Restore Policy | Policy gốc |
| DBA-OM-001 | DBA Service Catalog | Service mapping |
| DBA-OM-003 | DBA RACI Matrix | Trách nhiệm |
| DBA-POL-004 | Database Change Management Policy | Change trước backup |
| DBA-RBK-038 | Runbook Backup Verification | Runbook kiểm tra backup |
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
