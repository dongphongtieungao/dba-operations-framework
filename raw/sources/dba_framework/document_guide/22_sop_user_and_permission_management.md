# SOP User and Permission Management

## Thông tin tài liệu

| Trường | Giá trị |
|--------|--------|
| Mã tài liệu | DBA-SOP-022 |
| Loại tài liệu | SOP |
| Mức ưu tiên triển khai | 4 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Service Owner, Security Lead |
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

Chuẩn hóa việc tạo, sửa, khóa, xóa user và cấp, thu hồi quyền database để đảm bảo truy cập đúng người, đúng mục đích, đúng thời hạn và có audit đầy đủ.

## 2. Phạm vi

Áp dụng cho mọi yêu cầu liên quan đến tài khoản database, role, permission, service account, tài khoản khẩn cấp và quyền truy cập production hoặc non production.

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

1. Yêu cầu tạo user mới.
2. Yêu cầu cấp thêm quyền.
3. Yêu cầu thu hồi quyền.
4. Nhân sự nghỉ việc hoặc chuyển vai trò.
5. Cần tạo service account cho ứng dụng hoặc job.
6. Kết quả review quyền định kỳ phát hiện quyền không phù hợp.

## 5. Điều kiện tiên quyết

1. Có ticket yêu cầu hợp lệ.
2. Có thông tin requester, business owner, database, môi trường, loại quyền, thời hạn và lý do.
3. Có approval theo Access Control Policy.
4. Có danh mục role chuẩn hoặc permission matrix.
5. Có quyền DBA phù hợp để thực hiện thay đổi.

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

### 7.1. Phân loại yêu cầu

1. Xác định yêu cầu là tạo user, sửa user, cấp quyền, thu hồi quyền, reset password, khóa user hoặc xóa user.
2. Xác định môi trường production, staging, test hoặc development.
3. Xác định tài khoản là human account, service account hay emergency account.

### 7.2. Kiểm tra approval

1. Kiểm tra người phê duyệt có đúng vai trò theo RACI không.
2. Với production hoặc dữ liệu nhạy cảm, yêu cầu phê duyệt của service owner hoặc security theo policy.
3. Từ chối hoặc trả lại ticket nếu thiếu lý do, thiếu thời hạn hoặc quyền yêu cầu quá rộng.

### 7.3. Đối chiếu nguyên tắc least privilege

1. Không cấp quyền cao hơn nhu cầu thực tế.
2. Ưu tiên cấp role chuẩn thay vì gán quyền rời rạc trực tiếp cho từng user.
3. Với quyền tạm thời, phải có ngày hết hạn và cơ chế thu hồi.

### 7.4. Thực hiện thay đổi

1. Tạo hoặc cập nhật user theo naming convention.
2. Gán role hoặc permission theo permission matrix.
3. Áp dụng cấu hình password, MFA, certificate hoặc secret theo từng DBMS và nền tảng.
4. Không chia sẻ mật khẩu hoặc secret qua kênh không an toàn.

### 7.5. Kiểm tra sau thay đổi

1. Xác nhận user tồn tại và quyền đã cấp đúng phạm vi.
2. Kiểm tra user không có quyền vượt quá approval.
3. Với service account, xác nhận ứng dụng hoặc job kết nối thành công nếu có yêu cầu.

### 7.6. Ghi nhận evidence

1. Lưu câu lệnh đã thực hiện hoặc output script.
2. Lưu kết quả kiểm tra quyền sau thay đổi.
3. Cập nhật ticket và ghi rõ thời hạn thu hồi nếu là quyền tạm thời.

### 7.7. Lên lịch thu hồi nếu cần

1. Với quyền tạm thời, tạo reminder hoặc ticket thu hồi.
2. Với emergency access, bắt buộc review sau khi sự cố kết thúc.

## 8. Evidence bắt buộc

1. Ticket và approval.
2. Thông tin database, user, role, quyền được cấp hoặc thu hồi.
3. Lệnh hoặc script đã chạy.
4. Kết quả kiểm tra quyền sau thay đổi.
5. Ngày hết hạn nếu là quyền tạm thời.
6. Xác nhận hoàn tất từ requester nếu cần.

## 9. Rollback hoặc phương án khôi phục

Rollback là thu hồi quyền vừa cấp, khóa user vừa tạo hoặc khôi phục quyền cũ theo evidence trước thay đổi. Nếu rollback liên quan production, phải ghi nhận trong ticket và thông báo requester.

## 10. Điều kiện escalation

1. Yêu cầu quyền SYSDBA, SUPERUSER, sysadmin hoặc quyền tương đương trên production.
2. Yêu cầu truy cập dữ liệu nhạy cảm.
3. Thiếu approval nhưng requester yêu cầu xử lý gấp.
4. Phát hiện user dùng chung hoặc service account không có owner.
5. Phát hiện quyền vượt chuẩn trong quá trình kiểm tra.

## 11. Ghi chú theo từng DBMS

| DBMS | Lưu ý vận hành |
|---|---|
| SQL Server | Ưu tiên login, user, role và schema permission chuẩn. Kiểm tra sysadmin, db owner và SQL Agent proxy nếu liên quan. |
| PostgreSQL | Kiểm tra role, membership, default privileges, schema privileges và quyền trên sequence. |
| MySQL MariaDB | Kiểm tra user host, grants, plugin xác thực, quyền global và quyền database level. |
| Oracle | Kiểm tra user, profile, role, system privilege, object privilege và tablespace quota. |
| Azure SQL | Ưu tiên Azure AD, contained user, database role và hạn chế SQL authentication nếu policy yêu cầu. |
| Z DB | Tuân theo mô hình user, role và audit của nền tảng Z DB. |

## 12. Definition of Done

1. Yêu cầu đã được approval hợp lệ.
2. Quyền đã cấp hoặc thu hồi đúng phạm vi.
3. Không phát sinh quyền vượt chuẩn.
4. Evidence đã được lưu.
5. Ticket đã được cập nhật trạng thái hoàn tất hoặc chờ xác nhận.

## 13. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-POL-003 | Database Access Control Policy | Policy gốc |
| DBA-POL-002 | Database Security Policy | Bảo mật |
| DBA-OM-003 | DBA RACI Matrix | Trách nhiệm |
| DBA-POL-006 | Database Audit and Compliance Policy | Audit access |
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
