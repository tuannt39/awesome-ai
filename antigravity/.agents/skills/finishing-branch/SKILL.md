---
name: finishing-branch
description: Kích hoạt khi implementation hoàn thành, tất cả tests pass, và cần quyết định cách integrate work — hướng dẫn hoàn thành development work bằng cách trình bày structured options.
origin: superpowers
---

# Finishing a Development Branch — Hoàn thành Nhánh Phát triển

**Announce khi bắt đầu:** "Tôi đang dùng finishing-branch skill để hoàn thành work này."

**Core principle:** Verify tests → Detect environment → Present options → Execute choice → Clean up.

## Step 1: Verify Tests

**Trước khi trình bày options, verify tests pass:**

```bash
npm test / cargo test / pytest / go test ./...
```

**Nếu tests fail:**
```
Tests thất bại (<N> failures). Phải fix trước khi hoàn thành:

[Show failures]

Không thể proceed với merge/PR cho đến khi tests pass.
```

STOP. Không proceed sang Step 2.

## Step 2: Detect Environment

Xác định workspace state trước khi present options:

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
```

| State | Menu | Cleanup |
| --- | --- | --- |
| `GIT_DIR == GIT_COMMON` (normal repo) | 4 options tiêu chuẩn | Không có worktree |
| `GIT_DIR != GIT_COMMON`, named branch | 4 options tiêu chuẩn | Provenance-based |
| `GIT_DIR != GIT_COMMON`, detached HEAD | 3 options (không merge) | Không cleanup |

## Step 3: Determine Base Branch

```bash
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

## Step 4: Present Options

**Normal repo và named-branch worktree — trình bày chính xác 4 options này:**

```
Implementation hoàn thành. Bạn muốn làm gì?

1. Merge trực tiếp vào <base-branch>
2. Push và tạo Pull Request
3. Giữ branch như hiện tại (tôi sẽ xử lý sau)
4. Discard work này

Option nào?
```

**Detached HEAD — trình bày chính xác 3 options:**

```
Implementation hoàn thành. Bạn đang ở detached HEAD.

1. Push thành new branch và tạo Pull Request
2. Giữ như hiện tại (tôi sẽ xử lý sau)
3. Discard work này

Option nào?
```

## Step 5: Execute Choice

### Option 1: Merge Locally

```bash
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"
git checkout <base-branch>
git pull
git merge <feature-branch>
<test command>   # Verify tests trên merged result
# Sau khi merge success: cleanup worktree, rồi delete branch
git branch -d <feature-branch>
```

### Option 2: Push và Create PR

```bash
git push -u origin <feature-branch>
gh pr create --title "<title>" --body "$(cat <<'EOF'
## Summary
<2-3 bullets của những gì đã thay đổi>

## Test Plan
- [ ] <verification steps>
EOF
)"
```

**KHÔNG cleanup worktree** — user cần nó để iterate trên PR feedback.

### Option 3: Keep As-Is

Report: "Giữ branch <name>. Worktree được preserve tại <path>."

### Option 4: Discard

**Confirm trước:**
```
Điều này sẽ xóa vĩnh viễn:
- Branch <name>
- Tất cả commits: <commit-list>
- Worktree tại <path>

Gõ 'discard' để confirm.
```

Đợi exact confirmation. Nếu confirmed:
```bash
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"
# Cleanup worktree → delete branch
git branch -D <feature-branch>
```

## Step 6: Cleanup Workspace

**Chỉ chạy cho Options 1 và 4.** Options 2 và 3 luôn preserve worktree.

Kiểm tra provenance trước khi remove:
- Nếu worktree path dưới `.worktrees/`, `worktrees/`, `~/.config/superpowers/worktrees/` → Superpowers created → OK cleanup
- Nếu không → Host environment owns → KHÔNG remove

```bash
cd "$MAIN_ROOT"
git worktree remove "$WORKTREE_PATH"
git worktree prune  # Self-healing: clean up stale registrations
```

## Quick Reference

| Option | Merge | Push | Keep Worktree | Cleanup Branch |
| --- | --- | --- | --- | --- |
| 1. Merge locally | yes | — | — | yes |
| 2. Create PR | — | yes | yes | — |
| 3. Keep as-is | — | — | yes | — |
| 4. Discard | — | — | — | yes (force) |

## Common Mistakes — Tránh

- Bỏ qua test verification → Merge broken code
- Cleanup worktree cho Option 2 → Remove worktree user cần cho PR iteration
- Delete branch trước khi remove worktree → `git branch -d` fail
- Chạy `git worktree remove` từ bên trong worktree → Fail silently
- Không có confirmation cho discard → Accidentally delete work
