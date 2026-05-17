# Rule 04 — TDD Mandatory (Test-Driven Development Bắt buộc)

> **Nguồn:** Tổng hợp từ Superpowers TDD Iron Law + ECC tdd-workflow coverage requirements
> **Chế độ áp dụng:** Always On — Áp dụng khi viết bất kỳ code production nào

---

## IRON LAW — Luật Thép

```
TUYỆT ĐỐI KHÔNG VIẾT PRODUCTION CODE KHI CHƯA CÓ TEST THẤT BẠI TRƯỚC

Viết code trước khi có test? → XÓA ĐI. Bắt đầu lại.
```

**Không có ngoại lệ nào cho phép bỏ qua TDD nếu không có sự đồng ý của người dùng:**
- Không "giữ lại làm reference"
- Không "adapt trong khi viết test"
- Không "nhìn vào khi viết test"
- Delete nghĩa là delete thật sự

## Chu kỳ Red-Green-Refactor

```
RED:   Viết test thất bại  →  Kiểm chứng nó thất bại đúng cách
GREEN: Viết code tối thiểu →  Kiểm chứng tất cả test pass
REFACTOR: Dọn dẹp          →  Đảm bảo vẫn xanh
REPEAT: Tiếp tục cho feature tiếp theo
```

### Verify RED — KHÔNG BAO GIỜ BỎ QUA

```bash
# PHẢI chạy và xem test THẤT BẠI trước khi viết code
npm test path/to/test.test.ts
```

Xác nhận:
- Test thất bại (không phải lỗi syntax/runtime)
- Message thất bại là đúng kỳ vọng
- Thất bại vì feature chưa có (không phải typo)

### Verify GREEN — PHẢI kiểm tra

```bash
# Sau khi viết code, xác nhận test PASS
npm test path/to/test.test.ts
```

## Coverage Requirements

- **Tối thiểu 80% coverage** cho: unit tests + integration tests + E2E tests
- Tất cả edge cases phải được cover
- Error scenarios phải được test
- Boundary conditions phải được kiểm chứng

## Khi nào Bắt buộc Dùng TDD

- Tính năng mới
- Fix bug (viết test reproducing bug TRƯỚC KHI fix)
- Refactoring
- Thay đổi hành vi

**Khi nào có thể hỏi người dùng:** Throwaway prototypes, generated code, config files.

## Red Flags — STOP và Bắt đầu Lại

Nếu bắt gặp mình đang nghĩ những điều này:

| Suy nghĩ | Thực tế |
| --- | --- |
| "Quá đơn giản để cần test" | Code đơn giản cũng break. Test mất 30 giây. |
| "Test sau khi xong" | Test pass ngay = không chứng minh được gì |
| "Đã manually test rồi" | Manual ≠ systematic. Không lưu lại, không tái chạy được |
| "Xóa X giờ code là lãng phí" | Sunk cost fallacy. Code chưa verified là technical debt |
| "TDD quá dogmatic, cần pragmatic" | TDD IS pragmatic — tìm bug sớm hơn debug sau |

**Tất cả những suy nghĩ này = STOP. Xóa code. Bắt đầu lại với TDD.**

## Test Quality Standards

| Tiêu chí | Tốt | Xấu |
| --- | --- | --- |
| **Minimal** | Test một behavior | Test nhiều thứ cùng lúc |
| **Clear** | Tên mô tả hành vi | `test('test1')` |
| **Real** | Test code thực, không mock | Test mock behavior |
| **Isolated** | Mỗi test độc lập | Tests phụ thuộc vào nhau |

## Bug Fix Workflow

Bug phát hiện? → Viết test thất bại reproducing bug → Theo chu kỳ TDD → Test chứng minh fix và ngăn regression.

**Không bao giờ fix bug mà không có test.**
