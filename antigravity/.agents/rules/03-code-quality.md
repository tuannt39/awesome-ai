# Rule 03 — Code Quality (Chất lượng Code)

> **Nguồn:** Tổng hợp từ ECC AGENTS.md coding style, ECC RULES.md, ECC code-reviewer HIGH checklist
> **Chế độ áp dụng:** Always On — Áp dụng cho mọi code được viết hoặc sửa đổi

---

## Tiêu chuẩn Code Tuyệt đối

### Kích thước & Cấu trúc
- **Hàm:** Tối đa **50 dòng**. Hàm lớn hơn → Tách nhỏ.
- **File:** Tiêu chuẩn 200-400 dòng, tối đa **800 dòng**. Vượt quá → Extract modules.
- **Nesting:** Tối đa **4 tầng**. Sâu hơn → Dùng early returns, extract helpers.

### Immutability (BẮT BUỘC)

```typescript
❌ SAI: Mutation trực tiếp
function processUsers(users) {
  for (const user of users) {
    if (user.active) {
      user.verified = true;  // MUTATION!
      results.push(user);
    }
  }
}

✅ ĐÚNG: Immutable operations
function processUsers(users) {
  if (!users) return [];
  return users
    .filter(user => user.active && user.email)
    .map(user => ({ ...user, verified: true }));  // Tạo object mới
}
```

### Naming Convention
- Tên biến, hàm phải mô tả rõ ý nghĩa (không dùng `x`, `tmp`, `data` trong context không rõ ràng).
- File name: lowercase với hyphens cho readability.
- Hằng số: UPPER_SNAKE_CASE.

### Error Handling
- Xử lý lỗi ở mọi tầng — không nuốt lỗi im lặng (empty catch blocks).
- Provide user-friendly messages cho UI code.
- Log detailed context phía server.
- Không để promise rejections unhandled.

### Code Smells — Cờ đỏ cần fix

| Vấn đề | Hành động |
| --- | --- |
| Hàm >50 dòng | Tách thành các hàm nhỏ hơn |
| File >800 dòng | Extract module theo responsibility |
| Nesting >4 cấp | Dùng early returns |
| `console.log` trong production code | Xóa trước khi commit |
| Code bị comment out | Xóa (git history sẽ giữ lại nếu cần) |
| TODO không có ticket reference | Thêm issue number hoặc xóa |
| Magic numbers | Extract thành named constants |
| Duplicate code | Extract thành shared function/module |

## Tổ chức Code

- Tổ chức theo **feature/domain**, không phải theo kỹ thuật/loại file.
- Files thay đổi cùng nhau nên sống cùng nhau.
- High cohesion, low coupling — mỗi module có một trách nhiệm rõ ràng.
- Follow existing patterns của codebase trước khi tạo pattern mới.

## React/Frontend Patterns (khi áp dụng)

- `useEffect`/`useMemo`/`useCallback`: Đảm bảo dependency arrays đầy đủ.
- List rendering: Dùng unique, stable keys (không phải array index).
- Tránh state updates trong render — gây infinite loops.
- Tách client/server components rõ ràng (Next.js App Router).

## Node.js/Backend Patterns (khi áp dụng)

- Validate request body/params với schema trước khi xử lý.
- Rate limiting trên tất cả public endpoints.
- Tránh `SELECT *` hoặc queries không có LIMIT.
- Tránh N+1 queries — dùng JOIN hoặc batch loading.
- External HTTP calls phải có timeout configuration.
