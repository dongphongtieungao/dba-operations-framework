# DBA Operating Model

## Thông tin tài liệu

| Trường | Giá trị |
|--------|---------|
| Mã tài liệu | DBA-OM-002 |
| Loại tài liệu | Operating Model |
| Mức ưu tiên triển khai | 3 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Infra Lead |
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
| 0.2 | 2026-05-18 | DBA Team | Bổ sung on-call, communication channel, handover, chuẩn hóa metadata |

## 1. Mục đích

Tài liệu này định nghĩa mô hình vận hành DBA trong tổ chức. Mục tiêu là làm rõ vai trò DBA, cách tiếp nhận yêu cầu, cách xử lý service request, incident, change, escalation và cách phối hợp với application team, infrastructure team, security team, service owner và CAB.

## 2. Vai trò của DBA team

DBA team chịu trách nhiệm vận hành database theo hướng an toàn, ổn định, có kiểm soát và có bằng chứng. Vai trò bao gồm:

1. Quản lý cấu hình database.
2. Quản lý backup, restore, HA và DR.
3. Quản lý quyền truy cập database.
4. Giám sát sức khỏe database.
5. Điều tra sự cố database.
6. Hỗ trợ tuning hiệu năng.
7. Review thay đổi schema và SQL rủi ro cao.
8. Quản lý tri thức vận hành database.
9. Tự động hóa tác vụ lặp lại.
10. Cung cấp báo cáo vận hành định kỳ.

## 3. Các bên liên quan

### 3.1. Application team

1. Cung cấp yêu cầu nghiệp vụ.
2. Cung cấp thông tin ứng dụng.
3. Xác nhận kết quả sau restore, failover, change hoặc tuning.
4. Chịu trách nhiệm code ứng dụng và SQL thuộc phạm vi application.

### 3.2. Infrastructure team

1. Cung cấp compute, storage, network, load balancer và OS.
2. Hỗ trợ xử lý lỗi hạ tầng ảnh hưởng đến database.
3. Phối hợp trong HA, DR, capacity và patching.

### 3.3. Security team

1. Định nghĩa yêu cầu bảo mật.
2. Phê duyệt quyền nhạy cảm.
3. Review audit, encryption, key management và masking.
4. Hỗ trợ xử lý security incident.

### 3.4. Service owner

1. Chịu trách nhiệm cuối cùng về dịch vụ.
2. Phê duyệt rủi ro nghiệp vụ.
3. Xác nhận downtime, RPO, RTO và thay đổi lớn.

### 3.5. CAB

1. Review thay đổi quan trọng.
2. Phê duyệt change rủi ro cao.
3. Điều phối maintenance window.

## 4. Luồng xử lý service request

1. Requester tạo ticket.
2. Service desk hoặc DBA phân loại theo DBA Service Catalog (DBA-OM-001).
3. DBA kiểm tra input bắt buộc.
4. Nếu thiếu thông tin, DBA trả lại ticket kèm danh sách thông tin cần bổ sung.
5. Nếu có rủi ro hoặc liên quan production, DBA kiểm tra approval.
6. DBA thực hiện theo SOP hoặc runbook.
7. DBA kiểm tra kết quả.
8. Requester hoặc application owner xác nhận nếu cần.
9. DBA đính kèm evidence.
10. DBA đóng ticket.

## 5. Luồng xử lý incident

1. Incident được tạo từ monitoring, user report hoặc service desk.
2. DBA xác định severity.
3. DBA kiểm tra dashboard và log liên quan.
4. DBA áp dụng SOP xử lý tương ứng.
5. Nếu cần can thiệp khẩn cấp, DBA thực hiện mitigation được phép.
6. Nếu vượt quyền hoặc vượt phạm vi, DBA escalation tới service owner, infra hoặc security.
7. Sau khi khôi phục, DBA cập nhật timeline, impact và evidence.
8. Với P1 hoặc P2, DBA lập PIR.
9. DBA tạo action item để ngăn tái diễn.

## 6. Luồng xử lý change

1. Requester tạo RFC theo Database Change Management Policy (DBA-POL-004).
2. DBA review phạm vi database.
3. DBA kiểm tra script, rollback, maintenance window và impact.
4. Change rủi ro cao được đưa ra CAB.
5. DBA thực hiện thay đổi theo kế hoạch được duyệt.
6. DBA kiểm tra sau thay đổi.
7. Application team xác nhận chức năng nếu cần.
8. DBA đính kèm evidence.
9. Nếu lỗi xảy ra, DBA thực hiện rollback hoặc mitigation.
10. DBA cập nhật kết quả change.

## 7. Luồng xử lý emergency change

1. Incident nghiêm trọng hoặc nguy cơ mất dữ liệu được xác định.
2. DBA thông báo service owner và bên liên quan chính.
3. DBA xin phê duyệt khẩn cấp theo kênh được quy định.
4. DBA thực hiện thao tác khẩn cấp theo SOP phù hợp.
5. DBA lưu evidence càng đầy đủ càng tốt.
6. Sau khi ổn định, DBA tạo emergency change record nếu chưa có.
7. DBA lập post review bắt buộc.

## 8. Escalation model

### 8.1. Escalation kỹ thuật

1. DBA cấp 1 xử lý theo SOP.
2. DBA cấp 2 xử lý khi cần phân tích sâu hoặc thao tác rủi ro.
3. DBA lead tham gia khi có rủi ro production, dữ liệu hoặc HA DR.
4. Vendor hoặc platform team tham gia khi lỗi thuộc DBMS, cloud service hoặc nền tảng Z DB.

### 8.2. Escalation quản trị

1. Service owner tham gia khi có ảnh hưởng SLA, downtime, dữ liệu hoặc khách hàng.
2. CAB tham gia khi có change lớn.
3. Security tham gia khi có dữ liệu nhạy cảm, quyền bất thường hoặc audit issue.
4. Management tham gia khi có P1, mất dữ liệu hoặc vi phạm tuân thủ.

## 9. On-call và trực ca

### 9.1. Nguyên tắc

1. Database production phải có DBA trực hoặc có thể liên hệ ngoài giờ hành chính.
2. On-call rotation phải có lịch rõ ràng và được công bố trước ít nhất 1 tuần.
3. DBA on-call phải có quyền truy cập đủ để xử lý P1 và P2.
4. DBA on-call phải có SOP cho các tình huống ngoài giờ phổ biến.
5. On-call phải ghi nhận mọi sự cố xử lý ngoài giờ vào ticket ngay khi có thể.

### 9.2. Bàn giao ca

1. Bàn giao ca phải nêu rõ ticket đang xử lý.
2. Bàn giao phải nêu alert tồn đọng và risk hiện tại.
3. Bàn giao phải nêu change đã lên lịch trong ca tiếp theo.
4. Bàn giao nên có checklist hoặc form ngắn gọn.

## 10. Kênh liên lạc

| Mức độ | Kênh chính | Kênh phụ |
|--------|-----------|----------|
| P1 | Gọi điện thoại hoặc kênh khẩn cấp | Chat + Ticket |
| P2 | Chat kênh khẩn cấp | Ticket + Email |
| P3 | Ticket | Chat |
| P4, P5 | Ticket | Email |
| Báo cáo định kỳ | Email | Shared folder |
| Thảo luận kỹ thuật | Kênh chat nội bộ DBA | Meeting |

## 11. Operating cadence

### 11.1. Hằng ngày

1. Kiểm tra backup.
2. Kiểm tra alert tồn đọng.
3. Kiểm tra replication lag.
4. Kiểm tra capacity ngưỡng cao.
5. Kiểm tra incident P1, P2, P3.

### 11.2. Hằng tuần

1. Review change đã thực hiện.
2. Review ticket backlog.
3. Review quyền đặc biệt mới cấp.
4. Review top slow query hoặc incident lặp lại.

### 11.3. Hằng tháng

1. Báo cáo SLA.
2. Báo cáo backup và restore drill.
3. Báo cáo availability.
4. Báo cáo capacity.
5. Báo cáo automation progress.
6. Review risk register.

### 11.4. Hằng quý

1. Diễn tập restore.
2. Diễn tập failover hoặc DR theo phạm vi được chọn.
3. Review policy.
4. Review access privilege.
5. Review maturity level.

## 12. Chỉ số đánh giá

1. SLA compliance theo service type.
2. MTTA.
3. MTTR.
4. Change success rate.
5. Emergency change ratio.
6. Incident recurrence rate.
7. Ticket returned due to missing input.
8. SOP usage rate.
9. Runbook automation rate.
10. Evidence completion rate.

## 13. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-OF-001 | DBA Operations Framework Overview | Tổng quan framework |
| DBA-OM-001 | DBA Service Catalog | Danh mục dịch vụ |
| DBA-OM-003 | DBA RACI Matrix | Trách nhiệm |
| DBA-POL-004 | Database Change Management Policy | Quy trình change |
| DBA-POL-005 | Backup and Restore Policy | Backup restore |
| DBA-POL-007 | Database HA and DR Policy | HA DR |
