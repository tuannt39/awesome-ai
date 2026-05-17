---
name: tdd-workflow
description: Kích hoạt khi viết tính năng mới, fix bug, hoặc refactor code. Áp dụng TDD với coverage tối thiểu 80% bao gồm unit, integration, và E2E tests.
origin: merged-superpowers-ecc
---

# Test-Driven Development (TDD) Workflow

Skill hợp nhất từ Superpowers TDD Iron Law và ECC tdd-workflow.

## Iron Law — Không thể Thương lượng

```
TUYỆT ĐỐI KHÔNG CÓ PRODUCTION CODE NẾU CHƯA CÓ TEST THẤT BẠI TRƯỚC

Viết code trước test? → XÓA ĐI. Bắt đầu lại từ đầu.
```

## Chu kỳ Red-Green-Refactor

### RED — Viết Test Thất bại

Viết một test tối thiểu mô tả điều cần xảy ra.

```typescript
// VÍ DỤ TỐT: Tên rõ ràng, test hành vi thực tế, một việc
test('retries failed operations 3 times', async () => {
  let attempts = 0;
  const operation = () => {
    attempts++;
    if (attempts < 3) throw new Error('fail');
    return 'success';
  };
  const result = await retryOperation(operation);
  expect(result).toBe('success');
  expect(attempts).toBe(3);
});
```

**Yêu cầu:**
- Một behavior, một test
- Tên rõ ràng mô tả hành vi
- Test code thực (không mock nếu không cần thiết)

### Verify RED — KHÔNG BAO GIỜ BỎ QUA

```bash
npm test path/to/test.test.ts
```

Xác nhận:
- Test THẤT BẠI (không phải lỗi compile/syntax)
- Message thất bại là kỳ vọng
- Thất bại vì feature chưa có

**Test pass ngay lập tức?** Bạn đang test hành vi đã có. Fix test.

### GREEN — Viết Code Tối thiểu

Viết code đơn giản nhất để pass test — KHÔNG thêm gì khác.

```typescript
// VÍ DỤ TỐT: Vừa đủ để pass test
async function retryOperation<T>(fn: () => Promise<T>): Promise<T> {
  for (let i = 0; i < 3; i++) {
    try { return await fn(); }
    catch (e) { if (i === 2) throw e; }
  }
  throw new Error('unreachable');
}
```

Không thêm features, không refactor code khác, không "improve" ngoài test.

### Verify GREEN — PHẢI kiểm tra

```bash
npm test path/to/test.test.ts
```

Xác nhận: Test pass; các tests khác vẫn pass; output sạch.

### REFACTOR — Dọn dẹp

Chỉ sau khi xanh:
- Xóa duplication
- Cải thiện tên
- Extract helpers

Giữ tests xanh. Không thêm hành vi mới.

## Coverage Requirements

- **Tối thiểu 80% coverage** — unit + integration + E2E
- Tất cả edge cases phải được test
- Error scenarios phải được test
- Boundary conditions phải được kiểm chứng

## Các Loại Test

### Unit Tests
```typescript
describe('Button Component', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByText('Click me')).toBeInTheDocument()
  })

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn()
    render(<Button onClick={handleClick}>Click</Button>)
    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })
})
```

### Integration Tests (API Endpoints)
```typescript
describe('GET /api/users', () => {
  it('returns users successfully', async () => {
    const request = new NextRequest('http://localhost/api/users')
    const response = await GET(request)
    const data = await response.json()
    expect(response.status).toBe(200)
    expect(data.success).toBe(true)
  })

  it('validates query parameters', async () => {
    const request = new NextRequest('http://localhost/api/users?limit=invalid')
    const response = await GET(request)
    expect(response.status).toBe(400)
  })
})
```

### E2E Tests (Playwright)
```typescript
test('user can search and filter', async ({ page }) => {
  await page.goto('/')
  await page.fill('input[placeholder="Search..."]', 'keyword')
  await page.waitForTimeout(600)
  const results = page.locator('[data-testid="result-card"]')
  await expect(results).toHaveCount(5, { timeout: 5000 })
})
```

## Verification Checklist

Trước khi mark work là hoàn thành:

- [ ] Mọi function/method mới đều có test
- [ ] Đã xem từng test thất bại trước khi implement
- [ ] Mỗi test thất bại đúng lý do (feature missing, không phải typo)
- [ ] Đã viết code tối thiểu để pass từng test
- [ ] Tất cả tests pass
- [ ] Output sạch (không errors, warnings)
- [ ] Tests dùng real code (mocks chỉ khi thực sự cần thiết)
- [ ] Edge cases và error scenarios đã cover

Không thể check hết tất cả? Bạn đã bỏ qua TDD. Bắt đầu lại.

## Bug Fix Workflow

Bug phát hiện → Viết test thất bại reproducing bug → Theo chu kỳ TDD.
Test chứng minh fix và ngăn regression.
**Không bao giờ fix bug mà không có test.**

## Khi Gặp Khó Khăn

| Vấn đề | Giải pháp |
| --- | --- |
| Không biết cách test | Viết API bạn muốn có. Viết assertion trước. Hỏi. |
| Test quá phức tạp | Design quá phức tạp. Simplify interface. |
| Phải mock mọi thứ | Code quá tightly coupled. Dùng dependency injection. |
| Test setup quá lớn | Extract helpers. Vẫn phức tạp? Simplify design. |
