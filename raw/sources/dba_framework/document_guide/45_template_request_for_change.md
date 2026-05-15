---
status: draft
framework: DBA Operations Framework
document_group: Templates and Metrics
created_for: DBA professional operating system
language: vi
---

# Template Request for Change

## 1. Thông tin tài liệu

| Trường | Giá trị |
|---|---|
| Mã tài liệu | TPL-DBA-045 |
| Tên tài liệu | Template Request for Change |
| Nhóm tài liệu | Template and Metrics |
| Mức ưu tiên | 2 |
| Mục đích | Chuẩn hóa đề xuất thay đổi |
| Trạng thái | Draft |
| Owner đề xuất | DBA Team |
| Reviewer đề xuất | Service Owner, Infra Lead, Security Lead khi liên quan |
| Chu kỳ rà soát | Hằng quý hoặc sau mỗi sự cố lớn, thay đổi quy trình, thay đổi công cụ |

## 2. Mục đích

Tài liệu này cung cấp mẫu Request for Change cho các thay đổi database có kế hoạch. Mục tiêu là bảo đảm thay đổi được review trước, có kế hoạch triển khai, kế hoạch kiểm tra, rollback, đánh giá rủi ro, approval và evidence rõ ràng.

## 3. Khi nào sử dụng

| Trường hợp | Bắt buộc RFC |
|---|---:|
| Thay đổi schema trên production | Có |
| Thay đổi parameter, configuration, resource sizing | Có |
| Restore production hoặc ghi đè dữ liệu production | Có |
| Planned failover hoặc switchover | Có |
| Upgrade, patch, migration | Có |
| Thay đổi backup policy, retention, encryption | Có |
| Tạo index lớn có thể ảnh hưởng write hoặc lock | Có |
| Cấp quyền production dài hạn cho nhóm người dùng | Theo policy |

## 4. Mẫu RFC

| Trường | Nội dung cần điền | Bắt buộc | Ghi chú |
|---|---|---:|---|
| RFC ID | Mã change trong ITSM | Có |  |
| Related ticket | Service request, incident, problem, project ticket | Có nếu có |  |
| Change title | Tiêu đề thay đổi | Có | Ngắn gọn, dễ hiểu |
| Change type | Standard, Normal, Major, Planned Maintenance | Có | Không dùng Emergency Change cho thay đổi có kế hoạch |
| Requester | Người yêu cầu | Có |  |
| Change owner | Người chịu trách nhiệm thay đổi | Có | Thường là DBA lead hoặc service owner |
| Executor | Người thực hiện | Có |  |
| Reviewer | Người review kỹ thuật | Có | Peer review bắt buộc với production |
| Approver | Người phê duyệt | Có | Theo Change Management Policy |
| Application, service | Hệ thống bị ảnh hưởng | Có | Map CMDB nếu có |
| Environment | Production, Staging, UAT, Development, DR | Có |  |
| DBMS, version | Hệ quản trị và phiên bản | Có |  |
| Instance, database | Đối tượng thay đổi | Có |  |
| Business reason | Lý do nghiệp vụ | Có |  |
| Technical reason | Lý do kỹ thuật | Có |  |
| Desired outcome | Kết quả mong muốn | Có |  |
| Maintenance window | Thời gian triển khai | Có với production | Kèm timezone |
| Expected downtime | Dự kiến downtime | Có | Ghi 0 nếu không có downtime |
| Impact scope | Phạm vi ảnh hưởng | Có | User, app, batch, integration, report |
| Risk level | Low, Medium, High, Critical | Có | Theo ma trận rủi ro |

## 5. Kế hoạch triển khai

| Bước | Mô tả thao tác | Người thực hiện | Công cụ hoặc script | Thời lượng dự kiến | Evidence |
|---:|---|---|---|---|---|
| 1 | Pre check |  |  |  |  |
| 2 | Backup hoặc snapshot trước thay đổi |  |  |  |  |
| 3 | Thực hiện thay đổi |  |  |  |  |
| 4 | Post check |  |  |  |  |
| 5 | Xác nhận với application owner |  |  |  |  |
| 6 | Cập nhật ticket, CMDB, tài liệu nếu cần |  |  |  |  |

## 6. Kế hoạch kiểm tra trước thay đổi

| # | Nội dung kiểm tra | Kết quả mong muốn | Kết quả thực tế | Ghi chú |
|---:|---|---|---|---|
| 1 | Backup gần nhất hợp lệ | Backup thành công, đáp ứng RPO |  |  |
| 2 | Dung lượng đủ cho thay đổi | Disk, log, temp đủ |  |  |
| 3 | Replication hoặc HA ở trạng thái tốt | Replica đồng bộ trong ngưỡng cho phép |  |  |
| 4 | Không có incident đang mở có xung đột | Không có xung đột |  |  |
| 5 | Script đã được peer review | Đã review |  |  |
| 6 | Rollback plan đã được xác nhận | Có thể thực hiện |  |  |

## 7. Kế hoạch kiểm tra sau thay đổi

| # | Nội dung kiểm tra | Kết quả mong muốn | Kết quả thực tế | Evidence |
|---:|---|---|---|---|
| 1 | Database online | Online, healthy |  |  |
| 2 | Application connection | Kết nối thành công |  |  |
| 3 | Error log | Không có lỗi mới nghiêm trọng |  |  |
| 4 | Monitoring dashboard | Metric trong ngưỡng chấp nhận |  |  |
| 5 | Query hoặc nghiệp vụ kiểm thử | Pass |  |  |
| 6 | Backup, HA, replication nếu bị ảnh hưởng | Hoạt động bình thường |  |  |

## 8. Rollback plan

| Trường | Nội dung |
|---|---|
| Điều kiện kích hoạt rollback |  |
| Người quyết định rollback |  |
| Thời điểm quyết định rollback cuối cùng |  |
| Các bước rollback |  |
| Dữ liệu có nguy cơ mất |  |
| Thời gian rollback dự kiến |  |
| Cách kiểm tra sau rollback |  |
| Kênh thông báo rollback |  |

## 9. Đánh giá rủi ro

| Rủi ro | Khả năng xảy ra | Mức ảnh hưởng | Biện pháp giảm thiểu | Owner |
|---|---|---|---|---|
| Lock kéo dài | Low, Medium, High | Low, Medium, High, Critical | Chạy ngoài giờ, chia batch, timeout |  |
| Dung lượng log tăng cao | Low, Medium, High | Low, Medium, High, Critical | Kiểm tra log space, backup log, mở rộng disk |  |
| Query plan thay đổi xấu | Low, Medium, High | Low, Medium, High, Critical | Lưu plan, kiểm tra statistics, có rollback script |  |
| Replication lag | Low, Medium, High | Low, Medium, High, Critical | Theo dõi lag, tạm dừng batch nếu cần |  |
| Ứng dụng lỗi kết nối | Low, Medium, High | Low, Medium, High, Critical | Kiểm tra endpoint, connection pool, rollback |  |

## 10. Approval

| Vai trò | Người phê duyệt | Thời gian | Kết quả | Ghi chú |
|---|---|---|---|---|
| Service owner |  |  | Approved, Rejected |  |
| DBA lead |  |  | Approved, Rejected |  |
| Infra lead |  |  | Approved, Rejected, N/A |  |
| Security lead |  |  | Approved, Rejected, N/A |  |
| CAB |  |  | Approved, Rejected, N/A |  |

## 11. Tiêu chí đóng change

1. Toàn bộ bước triển khai và post check đã hoàn tất.
2. Không còn lỗi nghiêm trọng mới trên monitoring và error log trong thời gian quan sát đã thống nhất.
3. Evidence đã được đính kèm vào ticket.
4. Service owner hoặc requester xác nhận nếu cần nghiệm thu nghiệp vụ.
5. Nếu có deviation so với kế hoạch, phải ghi rõ nguyên nhân và action item.

## 99. Lịch sử thay đổi

| Version | Ngày | Người cập nhật | Nội dung thay đổi |
|---|---|---|---|
| 0.1 | YYYY-MM-DD | DBA Team | Khởi tạo bản nháp |
