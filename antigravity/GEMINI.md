# Google Antigravity — Agent Configuration

Đây là cấu hình trung tâm cho hệ sinh thái AI Agent-first tại dự án này.
Tổng hợp và chuyển đổi từ 4 nguồn cốt lõi:
- **Superpowers** (Jesse Vincent / Prime Radiant) — Phương pháp luận phát triển phần mềm TDD
- **Everything Claude Code / ECC** (Affaan Mustafa) — Hệ thống tối ưu hiệu suất toàn diện
- **Awesome Claude Skills** (ComposioHQ) — Thư viện kỹ năng mở rộng
- **Awesome Claude Code Subagents** (VoltAgent) — Tác nhân phụ chuyên biệt

---

## 📋 Hướng dẫn Vận hành cho Agent

### Ưu tiên Instruction:
1. **Yêu cầu trực tiếp của người dùng** (GEMINI.md, hội thoại trực tiếp) — Cao nhất
2. **Skills trong `.agents/skills/`** — Ghi đè hành vi mặc định khi áp dụng
3. **Rules trong `.agents/rules/`** — Chạy ngầm luôn luôn
4. **Hành vi mặc định của hệ thống** — Thấp nhất

### Cách truy cập Skills:
- **Trong Gemini/Antigravity:** Skills tự động kích hoạt thông qua cơ chế `activate_skill`.
- **Trong Claude Code:** Dùng công cụ `Skill`.
- **Nếu có khả năng 1% một skill áp dụng → BẮT BUỘC phải kích hoạt skill đó.**

---

## 🛡️ Nguyên tắc Cốt lõi Bắt buộc

1. **Agent-First** — Ủy quyền cho subagent chuyên biệt cho các tác vụ tên miền
2. **TDD (Test-Driven Development)** — Viết test trước, code sau; 80%+ coverage
3. **Security-First** — Không bao giờ nhượng bộ về bảo mật; validate tất cả đầu vào
4. **Immutability** — Luôn tạo object mới, không mutate trực tiếp
5. **Plan Before Execute** — Lập kế hoạch trước khi viết code cho tính năng phức tạp
6. **Root Cause First** — Tìm nguyên nhân gốc trước khi đề xuất bất kỳ fix nào

---

## 🔧 Tham chiếu nhanh Skills

| Tình huống | Skill cần kích hoạt |
| --- | --- |
| Bắt đầu tính năng mới | `.agents/skills/brainstorming/` |
| Cần lập kế hoạch triển khai | `.agents/skills/writing-plans/` |
| Viết code / fix bug | `.agents/skills/tdd-workflow/` |
| Gặp lỗi hoặc hành vi bất ngờ | `.agents/skills/systematic-debugging/` |
| Thực thi kế hoạch với subagents | `.agents/skills/subagent-driven-dev/` |
| Kiểm toán bảo mật | `.agents/skills/security-review/` |
| Đánh giá chất lượng code | `.agents/skills/code-review/` |
| Hoàn thành nhánh phát triển | `.agents/skills/finishing-branch/` |
| Kiểm chứng kết quả | `.agents/skills/verification-loop/` |
| Tạo skill mới | `.agents/skills/skill-creator/` |

---

## 🤖 Tham chiếu nhanh Subagents

| Agent | Khi nào dùng |
| --- | --- |
| `agents/planner.md` | Tính năng phức tạp, refactor lớn |
| `agents/architect.md` | Quyết định kiến trúc hệ thống |
| `agents/code-reviewer.md` | Sau khi viết/sửa code |
| `agents/tdd-guide.md` | Tính năng mới, fix bug |
| `agents/security-reviewer.md` | Code nhạy cảm, trước khi commit |
| `agents/debugger.md` | Lỗi phức tạp, debug nâng cao |
| `agents/python-pro.md` | Dự án Python |
| `agents/typescript-pro.md` | Dự án TypeScript/JavaScript |
| `agents/devops-engineer.md` | CI/CD, Docker, hạ tầng |
| `agents/research-analyst.md` | Nghiên cứu, thu thập thông tin |

---

## ⚡ Workflows Slash-Command

| Lệnh | Mô tả |
| --- | --- |
| `/new-feature` | Luồng đầy đủ: Brainstorm → Plan → TDD |
| `/bugfix` | Debug có hệ thống → TDD fix |
| `/code-review` | Đánh giá toàn diện code hiện tại |
| `/security-audit` | Quét bảo mật toàn diện |
| `/finish-branch` | Hoàn thành và tích hợp nhánh phát triển |

---

## 📁 Cấu trúc Thư mục

```
antigravity/
├── GEMINI.md                    # File này — Entry point chính
├── .agents/
│   ├── rules/                   # Luật lệ tự động (Always On)
│   │   ├── 00-project-defaults.md  # Ngôn ngữ, chính sách test, execution model
│   │   ├── 01-core-principles.md   # Agent-first, TDD, Security-first
│   │   ├── 02-security.md          # Checklist bảo mật, secrets, input validation
│   │   ├── 03-code-quality.md      # Style, immutability, kích thước file/hàm
│   │   ├── 04-tdd-mandatory.md     # Luật thép TDD: RED-GREEN-REFACTOR
│   │   ├── 05-git-workflow.md      # Conventional commits, branch, PR
│   │   └── GEMINI.md               # ⚠️ Legacy — nội dung đã được tích hợp vào 00-project-defaults.md
│   ├── skills/                  # Kỹ năng kích hoạt theo nhu cầu
│   │   ├── brainstorming/
│   │   ├── tdd-workflow/
│   │   ├── systematic-debugging/
│   │   ├── writing-plans/
│   │   ├── subagent-driven-dev/
│   │   ├── security-review/
│   │   ├── code-review/
│   │   ├── finishing-branch/
│   │   ├── verification-loop/
│   │   └── skill-creator/
│   └── workflows/               # Kịch bản thủ công (slash commands)
│       ├── new-feature.md
│       ├── bugfix.md
│       ├── code-review.md
│       ├── security-audit.md
│       └── finish-branch.md
└── agents/                      # Subagents chuyên biệt
    ├── planner.md
    ├── architect.md
    ├── code-reviewer.md
    ├── tdd-guide.md
    ├── security-reviewer.md
    ├── debugger.md
    ├── python-pro.md
    ├── typescript-pro.md
    ├── devops-engineer.md
    └── research-analyst.md
```

> **Lưu ý về `.agents/rules/GEMINI.md`:**  
> File này là phiên bản gốc dùng cho Claude Code (`CLAUDE.md`), đã được chuyển đổi sang chuẩn Antigravity và tích hợp toàn bộ nội dung vào `00-project-defaults.md`. File `GEMINI.md` bên trong thư mục con được engine Antigravity nhận diện là **entry point của thư mục đó** (không phải rule), vì vậy nó sẽ không được nạp tự động như các rule files khác. Bạn có thể xóa nó an toàn sau khi xác nhận `00-project-defaults.md` hoạt động đúng.
