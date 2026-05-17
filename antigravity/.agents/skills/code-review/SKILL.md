---
name: code-review
description: Kích hoạt ngay sau khi viết hoặc sửa code. Đánh giá chất lượng code, bảo mật, và maintainability. BẮT BUỘC cho mọi code changes.
origin: ecc
---

# Code Review Skill — Đánh giá Chất lượng Code

Skill đảm bảo tiêu chuẩn cao về chất lượng code và bảo mật.

## Review Process

Khi được kích hoạt:

1. **Thu thập context** — Chạy `git diff --staged` và `git diff` để xem tất cả changes. Nếu không có diff, check recent commits với `git log --oneline -5`.
2. **Hiểu scope** — Xác định files nào đã thay đổi, chúng liên quan đến feature/fix gì, và cách chúng kết nối.
3. **Đọc surrounding code** — Không review changes trong isolation. Đọc full file và hiểu imports, dependencies, và call sites.
4. **Apply review checklist** — Làm việc qua từng category từ CRITICAL đến LOW.
5. **Report findings** — Chỉ report issues bạn confident >80%.

## Confidence-Based Filtering

**QUAN TRỌNG**: Không flood review với noise. Apply những filters này:
- **Report** nếu >80% confident là real issue
- **Skip** stylistic preferences trừ khi vi phạm project conventions
- **Skip** issues trong unchanged code trừ khi là CRITICAL security issues
- **Consolidate** similar issues
- **Ưu tiên** issues có thể gây bugs, security vulnerabilities, hoặc data loss

## Review Checklist

### Security (CRITICAL) — PHẢI flag

- **Hardcoded credentials** — API keys, passwords, tokens trong source
- **SQL injection** — String concatenation trong queries
- **XSS vulnerabilities** — Unescaped user input rendered trong HTML/JSX
- **Path traversal** — User-controlled file paths không được sanitize
- **Authentication bypasses** — Missing auth checks
- **Exposed secrets in logs** — Logging sensitive data

### Code Quality (HIGH)

- **Large functions** (>50 lines) — Split thành smaller functions
- **Large files** (>800 lines) — Extract modules
- **Deep nesting** (>4 levels) — Dùng early returns
- **Missing error handling** — Unhandled promise rejections, empty catch blocks
- **Mutation patterns** — Prefer immutable operations
- **`console.log` statements** — Xóa trước khi merge
- **Missing tests** — New code paths không có test coverage

### Performance (MEDIUM)

- **Inefficient algorithms** — O(n²) khi O(n log n) hoặc O(n) khả thi
- **Unnecessary re-renders** — Missing React.memo, useMemo, useCallback
- **N+1 queries** — Fetching related data trong loop

### Best Practices (LOW)

- **TODO/FIXME không có ticket** — TODOs phải reference issue numbers
- **Poor naming** — Single-letter variables trong non-trivial contexts
- **Magic numbers** — Unexplained numeric constants

## Common False Positives — BỎ QUA Những điều này

Patterns LLM reviewers thường mis-flag. Skip nếu không có evidence cụ thể:

- "Consider adding error handling" trên một call mà error path được handle bởi caller hoặc framework
- "Missing input validation" khi function là internal và callers đã validate
- "Magic number" cho well-known constants: `200`, `404`, `1000ms`, `60`, `24`
- "Function too long" cho exhaustive switch statements, configuration objects, test tables
- "Missing JSDoc" trên single-purpose internal helpers có tên và signature self-describing

**Hỏi trước khi flag:** "Một senior engineer trên team này có thực sự change điều này trong review không?" Nếu không, skip.

## Review Output Format

Tổ chức findings theo severity:

```
[CRITICAL] Hardcoded API key trong source
File: src/api/client.ts:42
Issue: API key bị expose trong source code.
Fix: Move sang environment variable

[HIGH] Deep nesting (6 levels) gây khó đọc
File: src/utils/transform.ts:89-125
Issue: Nested if statements gây khó maintain.
Fix: Dùng early returns và extract helper functions
```

## Summary Format

Kết thúc mỗi review với:

```
## Review Summary

| Severity | Count | Status |
|----------|-------|--------|
| CRITICAL | 0     | pass   |
| HIGH     | 2     | warn   |
| MEDIUM   | 1     | info   |
| LOW      | 0     | note   |

Verdict: WARNING — 2 HIGH issues nên được resolve trước khi merge.
```

## Approval Criteria

- **Approve**: Không có CRITICAL hoặc HIGH issues. Clean review với zero findings là valid và expected.
- **Warning**: Chỉ có HIGH issues (có thể merge cẩn thận)
- **Block**: CRITICAL issues — phải fix trước khi merge

**Không withhold approval để trông rigorous.** Nếu diff clean, approve nó.

## Lưu ý cho AI-Generated Code

Khi review AI-generated changes, ưu tiên:
1. Behavioral regressions và edge-case handling
2. Security assumptions và trust boundaries
3. Hidden coupling hoặc accidental architecture drift
4. Unnecessary complexity tăng model cost
