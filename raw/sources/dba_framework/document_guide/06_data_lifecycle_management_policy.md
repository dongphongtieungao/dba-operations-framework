# Data Lifecycle Management Policy

## Thông tin tài liệu

| Trường | Giá trị |
|--------|---------|
| Mã tài liệu | DBA-POL-001 |
| Loại tài liệu | Policy |
| Mức ưu tiên triển khai | 2 |
| Owner | DBA Team |
| Reviewer | DBA Lead, Security Lead |
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
| 0.2 | 2026-05-18 | DBA Team | Bổ sung retention example table, compliance mention, escalation khi phát hiện P3 chưa masking, chuẩn hóa metadata |

## 1. Mục đích

Chính sách này quy định cách phân loại, lưu trữ, bảo vệ, archive, purge và hủy dữ liệu trong toàn bộ vòng đời dữ liệu. Mục tiêu là đảm bảo dữ liệu được quản lý đúng giá trị nghiệp vụ, đúng yêu cầu bảo mật, đúng chi phí lưu trữ và đúng yêu cầu tuân thủ.

## 2. Phạm vi

Áp dụng cho dữ liệu trong database production, non production, backup, replica, export file, archive storage và dữ liệu phục vụ báo cáo.

## 3. Phân loại dữ liệu

### 3.1. P0: Public

Dữ liệu công khai, nếu lộ không gây ảnh hưởng đáng kể.

### 3.2. P1: Internal

Dữ liệu nội bộ, không công khai ra ngoài tổ chức.

### 3.3. P2: Sensitive

Dữ liệu nhạy cảm, có thể ảnh hưởng khách hàng, vận hành hoặc tài chính nếu bị lộ.

### 3.4. P3: Highly confidential

Dữ liệu bí mật cao như PII, dữ liệu tài chính nhạy cảm, dữ liệu thanh toán, dữ liệu chịu yêu cầu tuân thủ hoặc dữ liệu trọng yếu của doanh nghiệp.

## 4. Nguyên tắc quản lý dữ liệu

1. Dữ liệu phải được phân loại trước khi triển khai database production.
2. Phân loại dữ liệu phải ảnh hưởng trực tiếp đến backup, retention, masking, encryption và access control.
3. Dữ liệu P2 và P3 phải được bảo vệ bằng quyền truy cập giới hạn và audit.
4. Dữ liệu non production không được dùng dữ liệu thật nếu có chứa P2 hoặc P3 mà chưa masking.
5. Dữ liệu hết hạn lưu trữ phải được archive hoặc purge theo quy định.
6. Việc hủy dữ liệu phải có evidence và không được làm theo thao tác cá nhân không kiểm soát.

## 5. Chính sách lưu trữ theo vòng đời

### 5.1. Hot data

1. Dữ liệu đang được truy cập thường xuyên.
2. Lưu trên storage hiệu năng cao.
3. Có backup theo RPO và RTO của hệ thống.
4. Có monitoring về growth, latency và transaction volume.

### 5.2. Warm data

1. Dữ liệu ít truy cập hơn nhưng vẫn cần query định kỳ.
2. Có thể lưu trên storage chi phí thấp hơn.
3. Có thể áp dụng partitioning hoặc compression nếu DBMS hỗ trợ.
4. Retention dựa trên yêu cầu nghiệp vụ và pháp lý.

### 5.3. Cold data

1. Dữ liệu hiếm khi truy cập.
2. Lưu trong archive storage hoặc object storage nếu phù hợp.
3. Không để cold data làm tăng chi phí và rủi ro vận hành production database.
4. Cần có quy trình restore hoặc truy xuất khi có yêu cầu audit.

## 6. Retention

### 6.1. Quy tắc chung

1. Retention phải được xác định theo loại dữ liệu, yêu cầu pháp lý, yêu cầu nghiệp vụ và chi phí.
2. Retention database không được quyết định độc lập bởi DBA nếu ảnh hưởng pháp lý hoặc nghiệp vụ.
3. Retention backup phải nhất quán với Backup and Restore Policy (DBA-POL-005).
4. Retention audit log phải nhất quán với Database Audit and Compliance Policy (DBA-POL-006).

### 6.2. Bảng retention tham khảo

Giá trị cụ thể phải được service owner và legal team phê duyệt cho từng hệ thống.

| Loại dữ liệu | Classification | Hot | Warm | Cold | Purge |
|---------------|---------------|-----|------|------|-------|
| Transaction data | P2 | 90 ngày | 1 năm | 3 năm | Theo pháp lý |
| Audit log | P2 | 1 năm | 3 năm | 5 năm | Theo pháp lý |
| Session và temp data | P0 | 30 ngày | N/A | N/A | 30 ngày |
| Customer PII | P3 | Active | 1 năm sau deactivate | 3 năm | Theo GDPR/PIPA |
| System log | P1 | 90 ngày | 1 năm | N/A | 1 năm |
| Report data | P1 | 1 năm | 3 năm | 5 năm | Theo nghiệp vụ |

### 6.3. Yêu cầu tối thiểu

1. Mỗi database production phải có retention được ghi nhận.
2. Dữ liệu P2 và P3 phải có quy định retention rõ ràng.
3. Dữ liệu hết retention phải được xử lý theo quy trình purge hoặc archive.
4. Không lưu dữ liệu nhạy cảm vô thời hạn nếu không có cơ sở phê duyệt.

## 7. Tuân thủ pháp lý

Khi làm việc với khách hàng hoặc dữ liệu thuộc phạm vi các quy định sau, retention và xử lý dữ liệu phải tuân thủ:

1. GDPR: Dữ liệu cá nhân công dân EU.
2. PIPA: Dữ liệu cá nhân theo luật Hàn Quốc.
3. PCI DSS: Dữ liệu thanh toán.
4. Quy định nội bộ tổ chức.

DBA không chịu trách nhiệm diễn giải pháp lý, nhưng phải áp dụng retention và purge theo yêu cầu đã được legal và compliance team phê duyệt.

## 8. Archive

1. Archive phải có owner phê duyệt.
2. Archive phải ghi nhận database, table, phạm vi thời gian, tiêu chí lọc, vị trí lưu và quyền truy cập.
3. Archive P2 hoặc P3 phải được mã hóa.
4. Archive phải có khả năng kiểm tra tính toàn vẹn.
5. Archive quan trọng phải được kiểm tra khả năng truy xuất định kỳ.

## 9. Purge

1. Purge production phải có ticket và approval.
2. Purge phải có tiêu chí rõ ràng.
3. Purge dữ liệu lớn phải đánh giá ảnh hưởng performance, lock, log growth và replication.
4. Purge nên thực hiện theo batch nếu khối lượng lớn.
5. Sau purge phải có evidence gồm số dòng xử lý, thời gian, lỗi nếu có và xác nhận kết quả.

## 10. Hủy dữ liệu an toàn

1. Hủy dữ liệu P2 và P3 phải đảm bảo không thể khôi phục bằng cách thông thường.
2. Hủy backup phải tuân thủ retention và approval.
3. Hủy dữ liệu phải lưu evidence.
4. Nếu sử dụng cloud hoặc managed platform, phải kiểm tra cơ chế deletion, retention và key destruction của nền tảng.

## 11. Xử lý vi phạm phân loại dữ liệu

Khi phát hiện dữ liệu P2 hoặc P3 ở non-production mà chưa masking:

1. DBA báo cáo ngay cho DBA Lead và Security team.
2. Tạm dừng truy cập non-production database nếu rủi ro cao.
3. Tạo ticket incident.
4. Xác định phạm vi và nguồn dữ liệu.
5. Thực hiện masking hoặc xóa dữ liệu vi phạm.
6. Lập PIR nếu dữ liệu đã bị truy cập không đúng quyền.

## 12. Liên kết với backup và restore

1. Phân loại dữ liệu quyết định encryption yêu cầu cho backup.
2. Retention dữ liệu quyết định retention backup.
3. RTO và RPO phải phản ánh mức độ quan trọng của dữ liệu.
4. Restore dữ liệu P2 và P3 sang non production phải qua masking nếu không có phê duyệt ngoại lệ.

## 13. Ngoại lệ

Ngoại lệ chỉ được chấp nhận khi:

1. Có lý do nghiệp vụ rõ ràng.
2. Có đánh giá rủi ro.
3. Có phê duyệt của service owner và security nếu liên quan dữ liệu nhạy cảm.
4. Có ngày hết hạn ngoại lệ.
5. Có kế hoạch đưa về trạng thái tuân thủ.

## 14. Chỉ số tuân thủ

1. Tỷ lệ database có phân loại dữ liệu.
2. Tỷ lệ database có retention được ghi nhận.
3. Tỷ lệ non production database chứa dữ liệu P2 hoặc P3 đã masking.
4. Số yêu cầu ngoại lệ còn hiệu lực.
5. Số purge hoặc archive có evidence đầy đủ.

## 15. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-POL-002 | Database Security Policy | Encryption và masking |
| DBA-POL-003 | Database Access Control Policy | Quyền truy cập theo classification |
| DBA-POL-005 | Backup and Restore Policy | Retention backup |
| DBA-POL-006 | Database Audit and Compliance Policy | Retention audit log |
