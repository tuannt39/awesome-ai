# Rule 05 — Git Workflow (Quy trình Git & Commit)

> **Nguồn:** Tổng hợp từ ECC git workflow, ECC RULES.md commit style, và Superpowers commit standards
> **Chế độ áp dụng:** Always On — Áp dụng cho mọi thao tác git

---

## Conventional Commits Format

```
<type>(<scope>): <description>

Types: feat, fix, refactor, docs, test, chore, perf, ci, style
```

**Ví dụ:**
```bash
feat(auth): add JWT refresh token rotation
fix(api): handle database timeout gracefully
refactor(users): extract validation logic into UserValidator class
docs(readme): update installation instructions for Windows
test(payments): add integration tests for Stripe webhook handler
chore(deps): update dependencies to latest versions
```

## Quy tắc Commit

- Mỗi commit phải đại diện cho một thay đổi logic hoàn chỉnh và có thể revert độc lập.
- Commit thường xuyên — sau mỗi "RED-GREEN" cycle trong TDD.
- Không commit code chưa có tests (ngoại trừ được người dùng cho phép).
- Không commit secrets, API keys, credentials.

## Branch Strategy

- Làm việc trên feature branches, không phải trực tiếp trên `main`/`master`.
- Sử dụng git worktrees cho parallel development (khi áp dụng Superpowers workflow).
- Branch name: `<type>/<short-description>` (ví dụ: `feat/user-auth`, `fix/payment-timeout`).

## Pull Request Standards

Mỗi PR phải có:
1. **Summary:** 2-3 bullets mô tả những gì đã thay đổi
2. **Test Plan:** Các bước kiểm chứng cụ thể
3. **Breaking Changes:** Nếu có, mô tả rõ ràng

```markdown
## Summary
- Added JWT refresh token rotation endpoint
- Updated auth middleware to validate refresh tokens
- Added integration tests covering token expiry scenarios

## Test Plan
- [ ] Login with valid credentials returns access + refresh tokens
- [ ] Expired access token with valid refresh token returns new pair
- [ ] Expired refresh token returns 401

## Breaking Changes
None
```

## PR Workflow

1. Analyze full commit history
2. Draft comprehensive summary
3. Include test plan
4. Push với `-u` flag: `git push -u origin <branch>`

## Agent Format Rules

- Agents lưu tại `agents/*.md`
- Mỗi file bao gồm YAML frontmatter với `name`, `description`, `tools`, `model`
- File names: lowercase với hyphens, phải match agent name

## Skill Format Rules

- Skills lưu tại `.agents/skills/<name>/SKILL.md`
- Mỗi skill bao gồm YAML frontmatter với `name`, `description`
- Skill bodies phải có: practical guidance, tested examples, và "When to Use" sections

## Hook Format Rules

- Hooks sử dụng matcher-driven JSON registration
- Matchers phải specific, không phải broad catch-alls
- Exit `1` chỉ khi blocking behavior là có chủ ý; otherwise exit `0`
- Error và info messages phải actionable
