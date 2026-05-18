# DBA Operations Framework Core Document Set

## Mục đích

Bộ tài liệu này là phiên bản lõi cho DBA Operations Framework, dùng để chuẩn hóa vận hành DBA theo hướng có chính sách, có quy trình, có trách nhiệm rõ ràng, có evidence và có khả năng tự động hóa.

## Danh mục tài liệu

| # | Mã tài liệu | Tên tài liệu | Loại | Phiên bản |
|---|-------------|---------------|------|-----------|
| 1 | DBA-OF-001 | DBA Operations Framework Overview | Framework | 0.2 |
| 2 | DBA-OM-001 | DBA Service Catalog | Operating Model | 0.2 |
| 3 | DBA-OM-002 | DBA Operating Model | Operating Model | 0.2 |
| 4 | DBA-OM-003 | DBA RACI Matrix | Operating Model | 0.2 |
| 5 | DBA-DG-001 | DBA Document Governance Standard | Standard | 0.2 |
| 6 | DBA-POL-001 | Data Lifecycle Management Policy | Policy | 0.2 |
| 7 | DBA-POL-002 | Database Security Policy | Policy | 0.2 |
| 8 | DBA-POL-003 | Database Access Control Policy | Policy | 0.2 |
| 9 | DBA-POL-004 | Database Change Management Policy | Policy | 0.2 |
| 10 | DBA-POL-005 | Backup and Restore Policy | Policy | 0.2 |
| 11 | DBA-POL-006 | Database Audit and Compliance Policy | Policy | 0.2 |
| 12 | DBA-POL-007 | Database HA and DR Policy | Policy | 0.2 |

## Gợi ý thứ tự triển khai

| Ưu tiên | Tài liệu | Lý do |
|---------|----------|-------|
| 1 | DBA-POL-005 Backup and Restore Policy | Nền tảng bảo vệ dữ liệu |
| 2 | DBA-POL-007 Database HA and DR Policy | Nền tảng sẵn sàng và khôi phục |
| 3 | DBA-POL-002 Database Security Policy | Bảo mật cơ bản |
| 4 | DBA-POL-004 Database Change Management Policy | Kiểm soát thay đổi |
| 5 | DBA-POL-003 Database Access Control Policy | Kiểm soát truy cập |
| 6 | DBA-OM-001 DBA Service Catalog | Chuẩn hóa dịch vụ |
| 7 | DBA-OM-002 DBA Operating Model | Mô hình vận hành |
| 8 | DBA-OM-003 DBA RACI Matrix | Trách nhiệm |
| 9 | DBA-DG-001 DBA Document Governance Standard | Quản trị tài liệu |
| 10 | DBA-POL-001 Data Lifecycle Management Policy | Vòng đời dữ liệu |
| 11 | DBA-POL-006 Database Audit and Compliance Policy | Kiểm toán |
| 12 | DBA-OF-001 DBA Operations Framework Overview | Tổng quan (review cuối) |

## Gợi ý sử dụng trong KMS

1. Lưu toàn bộ file trong thư mục docs/dba_operations_framework.
2. Dùng file này làm index chính.
3. Mỗi SOP hoặc runbook sau này phải liên kết ngược về policy và service catalog.
4. Mỗi ticket production nên tham chiếu service ID, SOP ID, runbook ID và evidence template.
5. Sử dụng YAML frontmatter theo chuẩn DBA-DG-001 để hỗ trợ agent tìm kiếm.
