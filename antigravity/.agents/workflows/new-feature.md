---
name: new-feature
command: /new-feature
description: Luồng phát triển tính năng mới toàn diện. Điều phối từ thiết kế, lập kế hoạch, phát triển TDD đến kiểm tra chất lượng.
---

# Workflow: /new-feature — Phát triển Tính năng Mới

Quy trình tự động hóa tích hợp để phát triển một tính năng mới từ ý tưởng đến sẵn sàng merge.

## Các Bước Điều phối (Orchestration Steps)

### Step 1: Brainstorming & Design Spec
- **Kích hoạt Skill:** `skills/brainstorming`
- **Hành động:** 
  1. Agent thu thập yêu cầu từ cuộc trò chuyện với người dùng.
  2. Đề xuất kiến trúc và các phương án thiết kế.
  3. Viết design spec vào `docs/specs/YYYY-MM-DD-<topic>-design.md`.
- **Gate:** Người dùng phê duyệt thiết kế để chuyển sang bước sau.

### Step 2: Implementation Planning
- **Kích hoạt Skill:** `skills/writing-plans`
- **Hành động:** 
  1. Phân tích spec đã được duyệt để tạo implementation plan chi tiết.
  2. Định nghĩa các task nhỏ gọn (bite-sized tasks) với tests và files cụ thể.
  3. Viết plan vào `docs/plans/YYYY-MM-DD-<topic>-plan.md`.

### Step 3: Subagent Execution (TDD)
- **Kích hoạt Skill:** `skills/subagent-driven-dev`
- **Hành động:**
  1. Tạo và phái các subagents chuyên biệt (ví dụ: `agents/typescript-pro`, `agents/python-pro`) để thực thi từng task.
  2. Áp dụng quy chuẩn `skills/tdd-workflow` cho từng task (RED-GREEN-REFACTOR).
  3. Thực hiện commits liên tục.

### Step 4: Verification & Reviews
- **Kích hoạt Skill:** `skills/verification-loop`
- **Kích hoạt Subagent:** `agents/code-reviewer`
- **Hành động:**
  1. Chạy full verification (build, lint, type-check, tests).
  2. `code-reviewer` chạy review độc lập toàn bộ code vừa thay đổi.

### Step 5: Merge & Clean Branch
- **Kích hoạt Skill:** `skills/finishing-branch`
- **Hành động:** Trình bày các tùy chọn merge / PR cho người dùng và dọn dẹp branch.
