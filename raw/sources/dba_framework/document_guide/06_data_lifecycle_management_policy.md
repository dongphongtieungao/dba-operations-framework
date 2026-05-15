# Data Lifecycle Management Policy

## Thông tin tài liệu

1. Mã tài liệu: DBA POL 001
2. Loại tài liệu: Policy
3. Mức ưu tiên triển khai: 2
4. Owner đề xuất: DBA Team
5. Trạng thái: Draft
6. Phiên bản: 0.1
7. Phạm vi áp dụng: SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid
8. Chu kỳ review: 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật

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
3. Retention backup phải nhất quán với Backup and Restore Policy.
4. Retention audit log phải nhất quán với Database Audit and Compliance Policy.

### 6.2. Yêu cầu tối thiểu

1. Mỗi database production phải có retention được ghi nhận.
2. Dữ liệu P2 và P3 phải có quy định retention rõ ràng.
3. Dữ liệu hết retention phải được xử lý theo quy trình purge hoặc archive.
4. Không lưu dữ liệu nhạy cảm vô thời hạn nếu không có cơ sở phê duyệt.

## 7. Archive

1. Archive phải có owner phê duyệt.
2. Archive phải ghi nhận database, table, phạm vi thời gian, tiêu chí lọc, vị trí lưu và quyền truy cập.
3. Archive P2 hoặc P3 phải được mã hóa.
4. Archive phải có khả năng kiểm tra tính toàn vẹn.
5. Archive quan trọng phải được kiểm tra khả năng truy xuất định kỳ.

## 8. Purge

1. Purge production phải có ticket và approval.
2. Purge phải có tiêu chí rõ ràng.
3. Purge dữ liệu lớn phải đánh giá ảnh hưởng performance, lock, log growth và replication.
4. Purge nên thực hiện theo batch nếu khối lượng lớn.
5. Sau purge phải có evidence gồm số dòng xử lý, thời gian, lỗi nếu có và xác nhận kết quả.

## 9. Hủy dữ liệu an toàn

1. Hủy dữ liệu P2 và P3 phải đảm bảo không thể khôi phục bằng cách thông thường.
2. Hủy backup phải tuân thủ retention và approval.
3. Hủy dữ liệu phải lưu evidence.
4. Nếu sử dụng cloud hoặc managed platform, phải kiểm tra cơ chế deletion, retention và key destruction của nền tảng.

## 10. Liên kết với backup và restore

1. Phân loại dữ liệu quyết định encryption yêu cầu cho backup.
2. Retention dữ liệu quyết định retention backup.
3. RTO và RPO phải phản ánh mức độ quan trọng của dữ liệu.
4. Restore dữ liệu P2 và P3 sang non production phải qua masking nếu không có phê duyệt ngoại lệ.

## 11. Ngoại lệ

Ngoại lệ chỉ được chấp nhận khi:

1. Có lý do nghiệp vụ rõ ràng.
2. Có đánh giá rủi ro.
3. Có phê duyệt của service owner và security nếu liên quan dữ liệu nhạy cảm.
4. Có ngày hết hạn ngoại lệ.
5. Có kế hoạch đưa về trạng thái tuân thủ.

## 12. Chỉ số tuân thủ

1. Tỷ lệ database có phân loại dữ liệu.
2. Tỷ lệ database có retention được ghi nhận.
3. Tỷ lệ non production database chứa dữ liệu P2 hoặc P3 đã masking.
4. Số yêu cầu ngoại lệ còn hiệu lực.
5. Số purge hoặc archive có evidence đầy đủ.
