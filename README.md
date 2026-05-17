# Awesome AI — Google Antigravity Ecosystem

Chào mừng bạn đến với repo hợp nhất hệ sinh thái **Agent-First Development** dựa trên chuẩn **Google Antigravity**.

Repo này đã tích hợp và chuyển đổi thành công 4 dự án AI hàng đầu:
1. **Superpowers** — Phương pháp phát triển phần mềm TDD và điều phối Subagents.
2. **Everything Claude Code (ECC)** — Hệ thống tối ưu chất lượng, bảo mật, và quản trị Agent.
3. **Awesome Claude Skills** — Thư viện kỹ năng mở rộng.
4. **Awesome Claude Code Subagents** — Các tác nhân phụ (subagents) chuyên trách.

Toàn bộ hệ sinh thái đã được migrate và tối ưu hóa tại thư mục:
👉 [**`awesome-ai/antigravity/`**](./antigravity/)

---

## 📁 Cấu trúc Hệ sinh thái Antigravity

Hệ thống hoạt động dựa trên cấu trúc chuẩn của Antigravity bao gồm:

- [**`GEMINI.md`**](./antigravity/GEMINI.md): Entry point trung tâm hướng dẫn vận hành hệ thống.
- [**`.agents/rules/`**](./antigravity/.agents/rules/): Các luật lệ hệ thống tự động chạy ngầm (Always On) như TDD Mandatory, Security, Code Quality, Git Workflow.
- [**`.agents/skills/`**](./antigravity/.agents/skills/): 10 kỹ năng chuyên biệt (TDD, Systematic Debugging, Brainstorming, Security/Code Review...) tự động nạp lũy tiến khi có yêu cầu (Progressive Disclosure).
- [**`.agents/workflows/`**](./antigravity/.agents/workflows/): Các Slash Commands mạnh mẽ (`/new-feature`, `/bugfix`, `/code-review`, `/security-audit`, `/finish-branch`) giúp tự động điều phối chuỗi hành động của Agent.
- [**`agents/`**](./antigravity/agents/): 10 Subagents chuyên môn hóa cao (Planner, Architect, TDD Guide, Security/Code Reviewer, Debugger, Python Pro, TypeScript Pro, DevOps...) chạy song song trên các context cô lập.

---

## 🎯 Bắt đầu Sử dụng

Để sử dụng hệ sinh thái này:
1. Trỏ Agent của bạn (Gemini CLI / Claude Code) vào thư mục [**`antigravity/`**](./antigravity/).
2. Agent sẽ tự động đọc [**`GEMINI.md`**](./antigravity/GEMINI.md) và kích hoạt bộ quy tắc chạy ngầm.
3. Gõ các lệnh điều hướng hoặc slash commands (ví dụ `/new-feature`, `/bugfix`) để bắt đầu luồng tự động hóa.
