---
name: systematic-debugging
description: Kích hoạt khi gặp bất kỳ bug, test failure, hoặc hành vi bất ngờ nào, TRƯỚC khi đề xuất fixes.
origin: superpowers
---

# Systematic Debugging — Gỡ lỗi Có Hệ thống

Fix ngẫu nhiên làm lãng phí thời gian và tạo ra bugs mới. Quick patches che giấu vấn đề cơ bản.

**Nguyên tắc cốt lõi:** LUÔN tìm root cause trước khi thử fix. Fix triệu chứng là thất bại.

## IRON LAW

```
TUYỆT ĐỐI KHÔNG FIX NẾU CHƯA ĐIỀU TRA ROOT CAUSE

Nếu chưa hoàn thành Phase 1, không được đề xuất fixes.
```

## Bốn Pha Bắt buộc

### Phase 1: Điều tra Root Cause

**TRƯỚC KHI thử bất kỳ fix nào:**

1. **Đọc Error Messages Kỹ lưỡng**
   - Đọc stack traces hoàn chỉnh
   - Ghi lại line numbers, file paths, error codes
   - Chúng thường chứa solution chính xác

2. **Reproduce Nhất quán**
   - Bạn có thể trigger nó một cách đáng tin cậy không?
   - Các bước chính xác là gì?
   - Nếu không reproducible → Thu thập thêm data, không đoán

3. **Kiểm tra Recent Changes**
   - Điều gì đã thay đổi có thể gây ra điều này?
   - `git diff`, recent commits
   - Dependencies mới, config changes, environmental differences

4. **Thu thập Evidence trong Multi-Component Systems**

   Với hệ thống nhiều component (CI → build → signing, API → service → database):

   TRƯỚC KHI đề xuất fixes, thêm diagnostic instrumentation:
   ```bash
   # Tại mỗi ranh giới component:
   echo "=== Data vào component A: ==="
   echo "ENV_VAR: ${ENV_VAR:+SET}${ENV_VAR:-UNSET}"
   
   # Chạy một lần để thu thập evidence
   # Phân tích evidence để xác định component thất bại
   # Điều tra component cụ thể đó
   ```

5. **Trace Data Flow**
   - Giá trị xấu bắt đầu từ đâu?
   - Cái gì gọi hàm này với giá trị xấu?
   - Tiếp tục trace lên cho đến khi tìm thấy source
   - Fix tại source, không phải tại triệu chứng

### Phase 2: Pattern Analysis

1. **Tìm Working Examples**
   - Locate similar working code trong cùng codebase
   - Điều gì works mà tương tự điều đang broken?

2. **So sánh với References**
   - Đọc reference implementation HOÀN TOÀN — Không skim
   - Hiểu pattern đầy đủ trước khi apply

3. **Identify Differences**
   - Điều gì khác nhau giữa working và broken?
   - List mọi sự khác biệt, dù nhỏ
   - Không assume "điều đó không thể quan trọng"

### Phase 3: Hypothesis và Testing

**Phương pháp khoa học:**

1. **Hình thành Single Hypothesis**
   - Nói rõ ràng: "Tôi nghĩ X là root cause vì Y"
   - Viết xuống. Cụ thể, không vague.

2. **Test Minimally**
   - Thực hiện thay đổi NHỎ NHẤT có thể để test hypothesis
   - Một biến mỗi lần
   - Không fix nhiều thứ cùng lúc

3. **Verify Trước khi Tiếp tục**
   - Nó hoạt động không? Có → Phase 4
   - Không hoạt động? Hình thành hypothesis MỚI
   - KHÔNG thêm fixes chồng lên nhau

### Phase 4: Implementation

1. **Tạo Failing Test Case** — Sử dụng tdd-workflow skill
2. **Implement Single Fix** — Address root cause được xác định
3. **Verify Fix** — Test pass? Tests khác vẫn pass? Vấn đề thực sự resolved?

**Nếu 3+ Fixes Thất bại:** STOP và Question Architecture:
- Pattern nào đang có vấn đề về shared state/coupling?
- Fixes có require "massive refactoring"?
- Mỗi fix có tạo symptoms mới không?

**→ Thảo luận với người dùng trước khi thử thêm fixes.**

## Red Flags — STOP và Theo Process

Nếu bắt gặp mình đang nghĩ:
- "Quick fix, điều tra sau"
- "Just thử change X và xem có work không"
- "Add nhiều changes, chạy tests"
- "Probably là X, để fix đó"
- "Không hiểu hết nhưng cái này có thể work"
- "Đề xuất solutions trước khi trace data flow"
- "One more fix attempt" (khi đã thử 2+ lần)

**TẤT CẢ những điều này = STOP. Return to Phase 1.**

## Quick Reference

| Phase | Key Activities | Success Criteria |
| --- | --- | --- |
| **1. Root Cause** | Đọc errors, reproduce, check changes, gather evidence | Hiểu WHAT và WHY |
| **2. Pattern** | Tìm working examples, so sánh | Xác định differences |
| **3. Hypothesis** | Hình thành theory, test minimally | Confirmed hoặc new hypothesis |
| **4. Implementation** | Tạo test, fix, verify | Bug resolved, tests pass |

## Related Skills

- **tdd-workflow** — Để tạo failing test case (Phase 4, Step 1)
- **verification-loop** — Verify fix đã work trước khi claim success
