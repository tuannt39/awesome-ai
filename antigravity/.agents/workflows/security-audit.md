---
name: security-audit
command: /security-audit
description: Quét bảo mật toàn diện cho codebase, tập trung vào OWASP Top 10, secrets leaks, và vulnerability checks.
---

# Workflow: /security-audit — Kiểm toán Bảo mật

Thực thi quy trình kiểm tra bảo mật tự động và rà soát codebase để phát hiện sớm các lỗ hổng nguy hiểm.

## Các Bước Điều phối (Orchestration Steps)

### Step 1: Static Analysis
- **Kích hoạt Subagent:** `agents/security-reviewer`
- **Hành động:** Quét static code analysis tìm các lỗi bảo mật điển hình (SQL injection, XSS, Path traversal, missing auth).

### Step 2: Secrets Scan
- **Hành động:** 
  1. Chạy quét toàn diện codebase tìm các chuỗi ký tự giống API Keys, private tokens, passwords.
  2. Verify `.gitignore` cấu hình đúng các tệp nhạy cảm (như `.env`).

### Step 3: Dependency Scan
- **Hành động:** Chạy `npm audit` hoặc `poetry run safety check` (tùy thuộc tech stack) để kiểm tra các thư viện bên thứ ba dễ bị tấn công.

### Step 4: Generate Security Report
- **Hành động:** Xuất báo cáo bảo mật chính thức. Bất kỳ lỗi CRITICAL nào phát hiện đều phải được báo cáo ngay lập tức cùng phương án remediate.
