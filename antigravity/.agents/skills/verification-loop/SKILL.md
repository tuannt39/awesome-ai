---
name: verification-loop
description: Hệ thống xác minh toàn diện cho các phiên làm việc. Kích hoạt sau khi hoàn thành tính năng, trước khi tạo PR, hoặc để đảm bảo quality gates (Build, Type, Lint, Test, Security).
origin: ecc
---

# Verification Loop Skill — Vòng lặp Xác minh

Hệ thống xác minh toàn diện cho phiên làm việc của agent.

## Khi nào Sử dụng

Kích hoạt skill này:
- Sau khi hoàn thành một tính năng hoặc thay đổi code lớn
- Trước khi tạo Pull Request (hoặc kích hoạt `finishing-branch`)
- Khi bạn muốn đảm bảo tất cả quality gates đều pass
- Sau khi refactor code

## Các Pha Xác minh

Bạn phải chạy các bước sau một cách tuần tự. Nếu bất kỳ bước nào thất bại, DỪNG LẠI và fix trước khi tiếp tục.

### Phase 1: Build Verification
```bash
# Kiểm tra project có build được không
npm run build 2>&1 | tail -20
# HOẶC
pnpm build 2>&1 | tail -20
```
Nếu build fail, DỪNG và fix trước khi tiếp tục.

### Phase 2: Type Check
```bash
# TypeScript projects
npx tsc --noEmit 2>&1 | head -30

# Python projects
pyright . 2>&1 | head -30
```
Báo cáo lỗi type. Fix các lỗi critical trước khi tiếp tục.

### Phase 3: Lint Check
```bash
# JavaScript/TypeScript
npm run lint 2>&1 | head -30

# Python
ruff check . 2>&1 | head -30
```

### Phase 4: Test Suite
```bash
# Chạy tests với coverage
npm run test -- --coverage 2>&1 | tail -50
```
Xác nhận coverage đạt ngưỡng tối thiểu (thường là 80%).

### Phase 5: Security Scan
```bash
# Quét tìm secrets bị rò rỉ (Chỉ chạy khi có terminal tool)
# Chú ý: Sử dụng tool grep_search nếu có thể thay vì bash grep

# Quét console.log dư thừa trong source
# Sử dụng tool grep_search cho "console.log" trong thư mục src/
```

### Phase 6: Diff Review
```bash
# Xem những gì đã thay đổi
git diff --stat
git diff HEAD~1 --name-only
```
Review từng file đã thay đổi để tìm:
- Unintended changes (thay đổi không mong muốn)
- Missing error handling
- Potential edge cases chưa xử lý

## Định dạng Output

Sau khi chạy tất cả các pha, tạo một báo cáo xác minh:

```markdown
# VERIFICATION REPORT

- **Build:**     [PASS/FAIL]
- **Types:**     [PASS/FAIL] (X lỗi)
- **Lint:**      [PASS/FAIL] (X cảnh báo)
- **Tests:**     [PASS/FAIL] (X/Y passed, Z% coverage)
- **Security:**  [PASS/FAIL] (X vấn đề)
- **Diff:**      [X files changed]

**Overall:**   [READY/NOT READY] cho PR

**Issues cần Fix:**
1. ...
2. ...
```

## Chế độ Liên tục (Continuous Mode)

Đối với các phiên làm việc dài, hãy chạy xác minh mỗi 15 phút hoặc sau mỗi thay đổi lớn:
- Sau khi hoàn thành mỗi hàm quan trọng
- Sau khi hoàn thành một component
- Trước khi chuyển sang task tiếp theo trong plan
