---
name: finish-branch
command: /finish-branch
description: Quy trình đóng và tích hợp nhánh phát triển (feature branch) sau khi hoàn tất kiểm thử.
---

# Workflow: /finish-branch — Hoàn tất Nhánh Phát triển

Hợp nhất các thay đổi cuối cùng, kiểm chứng chất lượng và tích hợp nhánh một cách an toàn.

## Các Bước Điều phối (Orchestration Steps)

### Step 1: Pre-requisite Verification
- **Kích hoạt Skill:** `skills/verification-loop`
- **Hành động:** Chạy kiểm thử toàn diện (build, tsc, lint, tests) để đảm bảo không merge code lỗi.
- **Gate:** Tất cả tests bắt buộc phải PASS.

### Step 2: Kích hoạt Finishing Branch Skill
- **Kích hoạt Skill:** `skills/finishing-branch`
- **Hành động:**
  1. Nhận diện môi trường git hiện tại (normal repo, worktree, detached HEAD).
  2. Xác định base branch (`main` hoặc `master`).

### Step 3: Presenting Integration Options
- Trình bày chính xác menu tùy chọn cho người dùng (Merge locally, Push & Create PR, Keep as-is, Discard).

### Step 4: Execute Choice & Cleanup
- Chạy logic tích hợp tương ứng và thực hiện dọn dẹp các git worktrees đã được tạo (pruning) nếu người dùng chọn merge hoặc discard.
