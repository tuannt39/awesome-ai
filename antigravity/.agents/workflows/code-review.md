---
name: code-review
command: /code-review
description: Kích hoạt quy trình đánh giá chất lượng mã nguồn tự động thông qua Code Reviewer subagent.
---

# Workflow: /code-review — Đánh giá Mã nguồn

Đánh giá chất lượng mã nguồn đối với các thay đổi hiện tại hoặc các commits gần đây.

## Các Bước Điều phối (Orchestration Steps)

### Step 1: Gather Git Context
- **Hành động:** 
  1. Quét qua các file thay đổi trong staging area bằng `git diff --staged`.
  2. Quét qua các thay đổi chưa staged bằng `git diff`.
  3. Nếu không có thay đổi, lấy 5 commits gần nhất bằng `git log --oneline -5`.

### Step 2: Kích hoạt Code Reviewer Subagent
- **Kích hoạt Subagent:** `agents/code-reviewer`
- **Hành động:** 
  1. Gửi diff/commits context cho code-reviewer.
  2. Chạy quét chất lượng code (nesting level, size, mutation patterns, error handling).

### Step 3: Run Verification
- **Kích hoạt Skill:** `skills/verification-loop`
- **Hành động:** Chạy song song type check và lint để bổ sung context cho review.

### Step 4: Trình báo kết quả
- **Hành động:** Báo cáo chi tiết các findings theo severity (CRITICAL, HIGH, MEDIUM, LOW) cùng đề xuất fix và bảng tóm tắt kèm Verdict (Thông qua / Cảnh báo / Block).
