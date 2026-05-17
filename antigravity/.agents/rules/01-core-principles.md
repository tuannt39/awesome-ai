# Rule 01 — Core Principles (Nguyên tắc Cốt lõi)

> **Nguồn:** Tổng hợp từ Everything Claude Code (ECC) AGENTS.md, Superpowers philosophy, và ECC RULES.md
> **Chế độ áp dụng:** Always On — Áp dụng cho mọi tác vụ trong phiên làm việc

---

## Phân cấp Ưu tiên Hành động

1. **Delegate trước** — Luôn ủy quyền cho subagent chuyên biệt khi có tác vụ tên miền cụ thể.
2. **Plan trước** — Lập kế hoạch đầy đủ trước khi thực thi bất kỳ tính năng phức tạp nào.
3. **Test trước** — Viết test trước khi viết implementation code (TDD).
4. **Security luôn luôn** — Bảo mật không phải tuỳ chọn; validate tất cả mọi đầu vào.
5. **Immutability** — Luôn tạo object mới, không bao giờ mutate state dùng chung.

## Must Always (Bắt buộc Làm)
- Ủy quyền cho subagent chuyên biệt cho các tác vụ tên miền.
- Viết test trước khi implement; đảm bảo coverage ≥ 80%.
- Validate tất cả đầu vào người dùng tại ranh giới hệ thống.
- Ưu tiên immutable updates, tránh mutating shared state.
- Tuân theo pattern đã có trong codebase trước khi tạo pattern mới.
- Giữ các thay đổi nhỏ gọn, có thể review, và mô tả rõ ràng.
- Giữ hàm nhỏ (<50 dòng) và file tập trung (<800 dòng).

## Must Never (Tuyệt đối Không Làm)
- Không đưa dữ liệu nhạy cảm (API keys, tokens, secrets, đường dẫn tuyệt đối) vào output.
- Không gửi thay đổi chưa có test.
- Không bỏ qua security checks hoặc validation hooks.
- Không tạo chức năng đã tồn tại mà không có lý do rõ ràng.
- Không deploy code mà chưa chạy test suite liên quan.
- Không tự mình cố fix trong khi AI agent đang loop — dừng lại và điều chỉnh.

## Nguyên tắc Kiến trúc

- **File organization:** Ưu tiên nhiều file nhỏ hơn ít file lớn. 200-400 dòng là chuẩn, 800 dòng là tối đa. Tổ chức theo feature/domain, không phải theo loại (type).
- **Error handling:** Xử lý lỗi ở mọi tầng. Message thân thiện cho UI, log chi tiết phía server. Không nuốt lỗi im lặng.
- **Input validation:** Validate tất cả đầu vào tại ranh giới hệ thống. Dùng schema-based validation. Fail fast với message rõ ràng. Không bao giờ tin tưởng dữ liệu bên ngoài.

## Agent Orchestration

Sử dụng subagents chủ động mà không cần yêu cầu từ người dùng:

- Yêu cầu tính năng phức tạp → **planner agent**
- Code vừa viết/sửa → **code-reviewer agent**
- Fix bug hoặc tính năng mới → **tdd-guide agent**
- Quyết định kiến trúc → **architect agent**
- Code nhạy cảm về bảo mật → **security-reviewer agent**

Sử dụng parallel execution cho các tác vụ độc lập — kích hoạt nhiều agents đồng thời.
