# DBA Operations Framework Overview

## Thông tin tài liệu

| Trường | Giá trị |
|--------|---------|
| Mã tài liệu | DBA-OF-001 |
| Loại tài liệu | Framework |
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
| 0.2 | 2026-05-18 | DBA Team | Bổ sung glossary, scope exclusion, cross-reference, chuẩn hóa metadata |

## 1. Mục đích

Tài liệu này định nghĩa tổng thể bộ khung vận hành DBA chuyên nghiệp. Mục tiêu là biến hoạt động DBA từ xử lý thủ công dựa vào kinh nghiệm cá nhân thành một hệ thống vận hành có chính sách, quy trình, tiêu chuẩn, mẫu biểu, runbook, bằng chứng và chỉ số đo lường.

Bộ khung này hướng tới nguyên tắc: mọi thao tác database quan trọng phải được kiểm soát, có thể kiểm toán, có thể đo lường và có khả năng tự động hóa.

## 2. Phạm vi

Áp dụng cho các hoạt động DBA liên quan đến:

1. Vận hành database hằng ngày.
2. Quản lý backup, restore, HA và DR.
3. Quản lý người dùng và phân quyền.
4. Quản lý thay đổi database.
5. Điều tra hiệu năng và sự cố.
6. Giám sát, cảnh báo và báo cáo.
7. Chuẩn hóa thiết kế schema, SQL, index và partition.
8. Tự động hóa tác vụ DBA bằng script, Ansible, Terraform, pipeline hoặc công cụ tương đương.
9. Quản lý tri thức vận hành database trong KMS.

## 3. Phạm vi loại trừ

Framework này không bao gồm:

1. Logic nghiệp vụ hoặc code ứng dụng. Trách nhiệm thuộc application team.
2. Quản lý hạ tầng OS, compute, storage, network không liên quan trực tiếp đến database. Trách nhiệm thuộc infra team.
3. Quản lý firewall, WAF, IDS, IPS ở tầng network. Trách nhiệm thuộc security và network team.
4. Quyết định kiến trúc ứng dụng như chọn DBMS, microservice hay monolith. DBA tham vấn nhưng không chịu trách nhiệm chính.
5. Quản lý license phần mềm database. DBA cung cấp thông tin sử dụng, quyết định mua sắm thuộc procurement hoặc management.

## 4. Nguyên tắc vận hành

### 4.1. Control first

Mọi thao tác trên production phải có kiểm soát. Không thực hiện thay đổi production khi thiếu ticket, approval, kế hoạch rollback hoặc bằng chứng kiểm tra sau thay đổi.

### 4.2. Evidence based operation

Mọi tác vụ quan trọng phải để lại bằng chứng. Evidence tối thiểu gồm ticket ID, người thực hiện, thời gian, phạm vi tác động, lệnh hoặc runbook đã chạy, kết quả trước và sau, log và xác nhận hoàn tất.

### 4.3. Automation by default

Các tác vụ lặp lại, có mẫu rõ ràng và rủi ro thao tác cao phải được ưu tiên tự động hóa. SOP là bước chuẩn hóa đầu tiên. Runbook tự động hóa là bước nâng cấp.

### 4.4. Least privilege

Con người không được giữ quyền cao thường trực trên production nếu không có lý do được phê duyệt. Quyền cao phải có thời hạn, có ticket, có audit và có thu hồi.

### 4.5. Restore proven, not backup assumed

Không coi backup là an toàn nếu chưa có restore test. Mục tiêu vận hành là khôi phục được dữ liệu theo RPO và RTO đã cam kết, không chỉ là backup job chạy thành công.

### 4.6. Standard before scale

Trước khi mở rộng số lượng database, số lượng môi trường hoặc số lượng DBA, phải chuẩn hóa service catalog, SOP, template, naming, monitoring và evidence.

## 5. Cấu trúc bộ tài liệu

Bộ tài liệu được tổ chức theo 6 nhóm.

### 5.1. Governance

Bao gồm các chính sách bắt buộc: Data Lifecycle (DBA-POL-001), Security (DBA-POL-002), Access Control (DBA-POL-003), Change Management (DBA-POL-004), Backup Restore (DBA-POL-005), Audit Compliance (DBA-POL-006), HA và DR (DBA-POL-007).

### 5.2. Operating model

Bao gồm Service Catalog (DBA-OM-001), Operating Model (DBA-OM-002), RACI (DBA-OM-003), luồng xử lý ticket, escalation, SLA, OLA và cơ chế phối hợp với app, infra, security.

### 5.3. Standards

Bao gồm tiêu chuẩn naming, schema design, SQL coding, indexing, partitioning, monitoring, alerting, capacity và evidence.

### 5.4. SOP

Bao gồm hướng dẫn thao tác chuẩn cho daily check, backup, restore, failover, troubleshooting, upgrade, corruption handling và decommission.

### 5.5. Runbook and automation

Bao gồm script, playbook, pipeline hoặc module tự động hóa cho các tác vụ lặp lại.

### 5.6. Evidence and continuous improvement

Bao gồm template evidence, restore drill report, failover drill report, PIR, KPI, risk register và monthly report.

> **DBMS Implementation**: Mỗi policy và standard có phần implementation cụ thể cho từng DBMS tại các Appendix: SQL Server (DBA-APP-001), PostgreSQL (DBA-APP-002), MySQL/MariaDB (DBA-APP-003), Oracle (DBA-APP-004), Azure SQL (DBA-APP-005), Z DB (DBA-APP-006).

## 6. Cách sử dụng trong công việc hằng ngày

### 6.1. Khi tiếp nhận service request

1. Xác định loại request trong DBA Service Catalog (DBA-OM-001).
2. Kiểm tra input bắt buộc.
3. Xác định SOP và runbook tương ứng.
4. Kiểm tra approval nếu request có rủi ro.
5. Thực hiện theo SOP hoặc runbook.
6. Lưu evidence.
7. Cập nhật ticket và đóng request.

### 6.2. Khi tiếp nhận incident

1. Xác định severity.
2. Kích hoạt SOP xử lý incident tương ứng.
3. Thu thập evidence trước khi can thiệp nếu không ảnh hưởng đến an toàn hệ thống.
4. Thực hiện mitigation.
5. Khôi phục dịch vụ.
6. Lập PIR nếu sự cố đạt ngưỡng yêu cầu.
7. Tạo action item để ngăn tái diễn.

### 6.3. Khi xử lý change

1. Kiểm tra RFC theo Database Change Management Policy (DBA-POL-004).
2. Review kế hoạch thực hiện.
3. Review kế hoạch rollback.
4. Review impact và maintenance window.
5. Thực hiện thay đổi theo SOP hoặc runbook.
6. Kiểm tra sau thay đổi.
7. Lưu evidence.
8. Cập nhật kết quả vào ticket.

## 7. Mức trưởng thành mục tiêu

### Level 1: Ad hoc DBA

Tác vụ phụ thuộc kinh nghiệm cá nhân. Tài liệu rời rạc. Evidence không nhất quán.

### Level 2: SOP based DBA

Có SOP cho tác vụ quan trọng. Vẫn còn nhiều thao tác thủ công.

### Level 3: Standardized DBA

Có policy, standard, template, peer review, change management và monitoring baseline.

### Level 4: Automated DBA

Các tác vụ lặp lại được chuyển thành runbook tự động hóa. Evidence được sinh tự động một phần.

### Level 5: Intelligent DBA Platform

Tích hợp ITSM, observability, automation, KMS và AI để phân loại ticket, gợi ý SOP, sinh RCA draft và đề xuất remediation.

## 8. Chỉ số đánh giá

1. Tỷ lệ service request có mapping tới SOP.
2. Tỷ lệ SOP có runbook tự động hóa.
3. Backup success rate.
4. Restore test success rate.
5. Change success rate.
6. Rollback rate.
7. MTTR theo từng loại incident.
8. Số lượng thao tác thủ công trên production.
9. Số lượng quyền cao thường trực.
10. Tỷ lệ tài liệu được review đúng hạn.

## 9. Liên kết tài liệu liên quan

| Mã tài liệu | Tên tài liệu | Mối liên hệ |
|-------------|---------------|-------------|
| DBA-OM-001 | DBA Service Catalog | Danh mục dịch vụ DBA |
| DBA-OM-002 | DBA Operating Model | Mô hình vận hành |
| DBA-OM-003 | DBA RACI Matrix | Phân quyền trách nhiệm |
| DBA-DG-001 | DBA Document Governance Standard | Quản trị tài liệu |
| DBA-POL-005 | Backup and Restore Policy | Backup và phục hồi |
| DBA-POL-007 | Database HA and DR Policy | HA và DR |
| DBA-POL-004 | Database Change Management Policy | Quản lý thay đổi |
| DBA-POL-002 | Database Security Policy | Bảo mật database |

## 10. Thuật ngữ

| Viết tắt | Nghĩa đầy đủ | Giải thích |
|----------|---------------|-----------|
| RPO | Recovery Point Objective | Mức mất dữ liệu tối đa chấp nhận được tính theo thời gian |
| RTO | Recovery Time Objective | Thời gian tối đa để khôi phục dịch vụ |
| MTTA | Mean Time To Acknowledge | Thời gian trung bình từ khi alert đến khi DBA xác nhận |
| MTTR | Mean Time To Resolve | Thời gian trung bình từ khi sự cố đến khi khôi phục |
| PIR | Post Incident Review | Phân tích sau sự cố |
| RCA | Root Cause Analysis | Phân tích nguyên nhân gốc rễ |
| RFC | Request For Change | Đề xuất thay đổi |
| CAB | Change Advisory Board | Hội đồng tư vấn thay đổi |
| SLA | Service Level Agreement | Cam kết mức dịch vụ với khách hàng |
| OLA | Operational Level Agreement | Cam kết mức dịch vụ nội bộ giữa các team |
| ITSM | IT Service Management | Quản lý dịch vụ CNTT |
| RACI | Responsible, Accountable, Consulted, Informed | Ma trận phân quyền trách nhiệm |
| SOP | Standard Operating Procedure | Quy trình vận hành chuẩn |
| HA | High Availability | Sẵn sàng cao |
| DR | Disaster Recovery | Khôi phục thảm họa |
| TDE | Transparent Data Encryption | Mã hóa dữ liệu trong suốt |
| TLS | Transport Layer Security | Bảo mật tầng truyền tải |
| PITR | Point In Time Recovery | Phục hồi theo thời điểm |
| WAL | Write Ahead Log | Log ghi trước của PostgreSQL |
| KMS | Knowledge Management System | Hệ thống quản lý tri thức |
