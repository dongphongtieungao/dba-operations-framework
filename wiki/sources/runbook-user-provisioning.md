---
id: runbook-user-provisioning
title: Runbook User Provisioning
type: source
created: 2026-05-18
updated: 2026-05-18
authors:
  - DBA Team
year: 2026
importance: 4
urls: []
raw_paths:
  - raw/sources/dba_framework/document_guide/37_runbook_user_provisioning.md
provenance: replayable
confidence: unverified
ingest_status: finalized
verify_status: skipped
findings: []
---

## Summary

Runbook User Provisioning chuẩn hóa việc tạo user, gán role, kiểm tra quyền, lưu audit evidence và cập nhật ticket. Trọng tâm của tài liệu là kiểm soát quyền truy cập theo approval, role chuẩn, thời hạn quyền production và bằng chứng trước/sau thay đổi. Đây là runbook quan trọng vì provisioning user liên quan trực tiếp đến bảo mật, audit, access review và nguyên tắc least privilege.

## Key Claims

- Script không được cấp quyền nếu thiếu approval hợp lệ. (confidence: high)
- Không nên cấp quyền cao hơn role chuẩn nếu không có exception được phê duyệt. (confidence: high)
- Quyền production cho human user cần có thời hạn hoặc được đưa vào review định kỳ. (confidence: high)
- Output phải thể hiện quyền trước và sau thay đổi để phục vụ audit. (confidence: high)
- Runbook cần bao phủ cả cấp quyền và thu hồi quyền, vì lifecycle truy cập không kết thúc ở bước grant. (confidence: high)

## Evidence

- Input bắt buộc gồm ticket_id, requester, approver, environment, dbms_type, database_name, account_type, role_requested và duration.
- Nguyên tắc tự động hóa yêu cầu approval, role chuẩn, thời hạn quyền production cho human user, audit mọi thay đổi và output quyền trước/sau.
- Luồng thực thi gồm validate ticket/approval, kiểm tra account, kiểm tra role chuẩn, kiểm tra quyền hiện tại, tạo user nếu được phép, gán role/permission, chạy verification query, ghi audit evidence và cập nhật ticket.
- Verification output gồm account_name, database_name, granted_role, previous_permission, current_permission, expiration_date và evidence_path.

## Related Concepts

- [[concepts/database-user-provisioning-runbook]]
- [[concepts/sql-server-security-auditing]]
- [[concepts/dba-evidence-driven-operations]]
- [[concepts/dba-policy-sop-runbook-layering]]
- [[concepts/dba-automation-lifecycle]]

## Related Sources

- [[sources/dba-operations-framework]]
- [[sources/runbook-database-provisioning]]

## People

Không có cá nhân cụ thể được tạo hồ sơ trong lần nạp này.

## Open Questions

- Cần định nghĩa danh mục role chuẩn cho từng DBMS và từng môi trường.
- Cần bổ sung logic xử lý exception, break-glass access và quyền tạm thời.
- Cần nối runbook với access review định kỳ để tự động phát hiện quyền hết hạn hoặc quyền lệch chuẩn.

## Notes

