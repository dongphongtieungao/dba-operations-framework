# DBA RACI Matrix

## Thông tin tài liệu

1. Mã tài liệu: DBA OM 003
2. Loại tài liệu: Operating Model
3. Mức ưu tiên triển khai: 3
4. Owner đề xuất: DBA Team
5. Trạng thái: Draft
6. Phiên bản: 0.1
7. Phạm vi áp dụng: SQL Server, Azure SQL, PostgreSQL, MySQL, MariaDB, Oracle, Z DB và các nền tảng database trong môi trường hybrid
8. Chu kỳ review: 6 tháng hoặc sau sự cố nghiêm trọng, thay đổi kiến trúc, thay đổi quy định bảo mật

## 1. Mục đích

Tài liệu này làm rõ trách nhiệm giữa DBA team và các bên liên quan trong những hoạt động vận hành database. Mục tiêu là tránh tình trạng không rõ ai thực hiện, ai phê duyệt, ai chịu trách nhiệm cuối cùng và ai cần được thông báo.

## 2. Quy ước RACI

1. R là Responsible, người thực hiện chính.
2. A là Accountable, người chịu trách nhiệm cuối cùng.
3. C là Consulted, bên cần tham vấn trước hoặc trong khi thực hiện.
4. I là Informed, bên cần được thông báo.

## 3. Vai trò sử dụng trong ma trận

1. DBA: Database Administrator hoặc DBA team.
2. DBA Lead: Người phụ trách kỹ thuật DBA.
3. App Team: Đội phát triển hoặc vận hành ứng dụng.
4. Infra Team: Đội hạ tầng, hệ điều hành, storage, network, cloud.
5. Security Team: Đội bảo mật và tuân thủ.
6. Service Owner: Chủ sở hữu dịch vụ nghiệp vụ hoặc hệ thống.
7. CAB: Change Advisory Board.
8. Service Desk: Đội tiếp nhận và điều phối ticket.
9. Vendor hoặc Platform Team: Nhà cung cấp hoặc đội nền tảng như Z DB, cloud managed database.

## 4. Ma trận RACI theo hoạt động

### 4.1. Tạo database mới

1. DBA: R
2. DBA Lead: C
3. App Team: C
4. Infra Team: C
5. Security Team: C nếu dữ liệu nhạy cảm
6. Service Owner: A
7. CAB: I nếu production hoặc change lớn
8. Service Desk: I

### 4.2. Cấp quyền database

1. DBA: R
2. DBA Lead: C nếu quyền cao
3. App Team: C
4. Security Team: A nếu quyền production hoặc dữ liệu nhạy cảm
5. Service Owner: A với quyền nghiệp vụ
6. Service Desk: I

### 4.3. Thu hồi quyền

1. DBA: R
2. Security Team: A
3. App Team: C nếu là service account
4. Service Owner: C
5. Service Desk: I

### 4.4. Backup định kỳ

1. DBA: R
2. DBA Lead: A
3. Infra Team: C nếu liên quan storage hoặc backup platform
4. Security Team: C nếu liên quan encryption hoặc key
5. Service Owner: I

### 4.5. Restore dữ liệu

1. DBA: R
2. DBA Lead: A nếu production hoặc restore rủi ro cao
3. App Team: C và xác nhận dữ liệu
4. Infra Team: C nếu cần storage hoặc server
5. Security Team: C nếu dữ liệu nhạy cảm
6. Service Owner: A với restore production
7. CAB: C nếu restore có downtime hoặc rủi ro lớn

### 4.6. Point in time recovery

1. DBA: R
2. DBA Lead: A
3. App Team: C
4. Service Owner: A với lựa chọn recovery point
5. Security Team: C nếu có dữ liệu nhạy cảm
6. CAB: C nếu production

### 4.7. Failover khẩn cấp

1. DBA: R
2. DBA Lead: A
3. Infra Team: C
4. App Team: C và xác nhận ứng dụng
5. Service Owner: A với quyết định nghiệp vụ
6. Security Team: I hoặc C nếu có rủi ro bảo mật
7. CAB: I sau khi thực hiện
8. Vendor hoặc Platform Team: C nếu là managed platform

### 4.8. Switchover có kế hoạch

1. DBA: R
2. DBA Lead: A
3. App Team: C
4. Infra Team: C
5. Service Owner: A
6. CAB: A với change production
7. Service Desk: I

### 4.9. Review schema change

1. App Team: R với nội dung thay đổi
2. DBA: R với review database impact
3. DBA Lead: C nếu rủi ro cao
4. Service Owner: A với thay đổi nghiệp vụ
5. CAB: A nếu production change
6. Security Team: C nếu dữ liệu nhạy cảm

### 4.10. Triển khai schema change

1. DBA: R nếu DBA thực thi script
2. App Team: R nếu deployment pipeline thuộc app team
3. DBA Lead: C
4. CAB: A với production change
5. Service Owner: A với rủi ro nghiệp vụ
6. Service Desk: I

### 4.11. Điều tra query chậm

1. DBA: R với phân tích database
2. App Team: R với phân tích code và SQL nghiệp vụ
3. DBA Lead: C nếu ảnh hưởng production nghiêm trọng
4. Infra Team: C nếu liên quan tài nguyên hệ thống
5. Service Owner: I

### 4.12. Xử lý blocking hoặc deadlock

1. DBA: R
2. App Team: C
3. DBA Lead: A nếu cần kill session production
4. Service Owner: C nếu có ảnh hưởng nghiệp vụ
5. CAB: I nếu phát sinh emergency change

### 4.13. RCA hoặc PIR sau sự cố

1. DBA: R với phần database
2. App Team: R với phần application
3. Infra Team: R với phần hạ tầng
4. Security Team: R nếu có yếu tố bảo mật
5. Service Owner: A
6. DBA Lead: C
7. Management: I với P1 hoặc P2

### 4.14. Review quyền định kỳ

1. DBA: R với dữ liệu quyền hiện tại
2. Security Team: A
3. App Team: C với service account
4. Service Owner: A với quyền nghiệp vụ
5. Service Desk: I

### 4.15. Decommission database

1. DBA: R
2. App Team: C
3. Infra Team: C
4. Security Team: C nếu có dữ liệu nhạy cảm
5. Service Owner: A
6. CAB: A nếu production
7. Service Desk: I

## 5. Quy tắc áp dụng

1. Mỗi ticket production phải xác định ít nhất một A.
2. Không để một hoạt động rủi ro cao chỉ có R mà không có A.
3. Người thực hiện và người phê duyệt không nên là cùng một người với change rủi ro cao.
4. RACI phải được cập nhật khi tổ chức thay đổi vai trò hoặc quy trình.
5. Nếu có xung đột trách nhiệm, DBA Lead và Service Owner phải thống nhất trước khi thực hiện.

## 6. Evidence liên quan RACI

1. Approval record.
2. Ticket history.
3. Comment xác nhận của application owner.
4. CAB approval nếu có.
5. Security approval nếu có.
6. Operation evidence sau thực hiện.
