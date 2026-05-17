---
apply: always
priority: 0
description: "Thiết lập mặc định cho dự án: ngôn ngữ, chính sách test, mô hình thực thi, và tiêu chuẩn hoàn thành tác vụ."
---

# Rule 00 — Project Defaults (Thiết lập Mặc định Dự án)

> **Phạm vi áp dụng:** Toàn bộ phiên làm việc. Rule này được nạp đầu tiên, trước tất cả các rules khác, và hoạt động liên tục ngầm bên dưới mọi tác vụ.

---

## 🌐 Ngôn ngữ Mặc định

- Ngôn ngữ chính: **Tiếng Việt**.
- Mọi phản hồi, giải thích, tiến trình, kế hoạch, và báo cáo cuối cùng phải được viết bằng tiếng Việt.
- Sử dụng ngôn ngữ rõ ràng, tự nhiên, súc tích.
- Mọi kết luận đều phải dựa trên bằng chứng thực tế (evidence-based).

---

## 📋 Thông tin Dự án (Project Metadata)

> **Hướng dẫn:** Khi áp dụng hệ sinh thái này cho một dự án cụ thể, hãy điền các thông tin sau vào file `GEMINI.md` tại thư mục gốc của dự án đó:

```yaml
stack:             # Ví dụ: Python + FastAPI, TypeScript + Next.js
package_manager:   # Ví dụ: poetry, npm, pnpm
main_apps:         # Danh sách service/app chính
important_folders: # Ví dụ: src/, tests/, docs/
commands:
  install:         # Ví dụ: poetry install / npm install
  dev:             # Ví dụ: uvicorn main:app --reload / npm run dev
  test:            # Ví dụ: pytest / npm test
  lint:            # Ví dụ: ruff check . / npm run lint
  typecheck:       # Ví dụ: mypy . / tsc --noEmit
  build:           # Ví dụ: docker build / npm run build
```

---

## 🛡️ Nguyên tắc Thực hành Bắt buộc

Những nguyên tắc sau được áp dụng ở mọi thời điểm, bổ sung cho các rules còn lại:

1. **Hiểu vấn đề thực sự trước khi thay đổi bất cứ thứ gì.** Không nhảy vào viết code khi chưa rõ yêu cầu.
2. **Thực hiện thay đổi nhỏ, rõ ràng, có thể kiểm chứng được.** Không mở rộng phạm vi trừ khi thật sự cần thiết.
3. **Tuân theo patterns hiện tại của dự án** trừ khi có lý do kỹ thuật chính đáng để thay đổi.
4. **Ưu tiên giải pháp đơn giản nhất có thể hoạt động.** Chống xu hướng over-engineering.
5. **Sửa tận gốc nguyên nhân (root cause)**, không vá víu triệu chứng bề mặt — xem `.agents/skills/systematic-debugging`.
6. **Bằng chứng quan trọng hơn khẳng định.** Không bao giờ kết luận mà không có evidence.
7. **Main Agent là Orchestrator và người ra quyết định cuối cùng.** Không ủy quyền toàn bộ quyết định cho subagents.

---

## 📁 Quy tắc Kho lưu trữ (Repository Rules)

- Không tạo git commits trừ khi người dùng yêu cầu rõ ràng.
- Không dùng git worktrees trừ khi người dùng yêu cầu rõ ràng.
- Làm việc trong thư mục hiện tại của dự án.
- Không tạo hoặc chỉnh sửa tests, fixtures, mock-heavy files trừ khi được ủy quyền rõ ràng.
- Chạy tests hiện có là được phép khi hữu ích.

---

## 📝 Chính sách Viết Tests (TDD-Aware Policy)

> **Lưu ý quan trọng:** Khi người dùng kích hoạt Skill `tdd-workflow` hoặc Workflow `/new-feature`, chính sách TDD đầy đủ (viết test TRƯỚC, viết code SAU) từ rule `04-tdd-mandatory.md` sẽ được áp dụng và **ghi đè** chính sách mặc định này.

**Hành vi mặc định (khi không kích hoạt TDD Workflow):**
- Không tự động viết code test mới.
- Không chủ động thêm tests cho bug fixes, refactors, hay features mà người dùng chưa yêu cầu.
- Không hiểu "kiểm tra", "xác minh", "đảm bảo hoạt động đúng" là yêu cầu viết tests.

**Chỉ viết hoặc chỉnh sửa tests khi người dùng nói rõ ràng:**
> "viết tests" / "thêm tests" / "tạo tests" / "TDD" / "test file" / "spec file" / "bao phủ code này bằng tests"

**Phương pháp kiểm chứng không cần viết tests mới (ưu tiên theo thứ tự):**
1. Build thành công
2. Typecheck (`mypy`, `tsc --noEmit`)
3. Lint (`ruff`, `eslint`)
4. Chạy tests sẵn có nếu không tốn kém
5. Tái hiện thủ công / kiểm tra logs / command output

Nếu verification bị hạn chế do không được phép viết tests, **ghi rõ điều đó** trong báo cáo cuối mà không coi đó là thất bại.

---

## ⚙️ Mô hình Thực thi (Execution Model)

**Phân loại công việc trước khi thực thi:**
- **Independent tracks**: Có thể song song hóa an toàn.
- **Shared-state tracks**: Bắt buộc tuần tự hoặc dùng locking.
- **Integration work**: Chỉ sau khi tất cả independent tracks hoàn thành.
- **Final verification**: Luôn tuần tự và sau cùng.

**Nguyên tắc:**
- Song song hóa: discovery, phân tích, review, lập kế hoạch verification.
- Tuần tự: khi có dependencies, shared state, hoặc rủi ro conflict.
- Không chọn tuần tự chỉ vì thói quen — phải phân tích thực sự.

---

## 🔒 Cô lập Phiên & Bảo vệ Dữ liệu

- Coi dữ liệu phiên là bị giới hạn bởi phạm vi của phiên theo mặc định.
- Không xóa, reset, ghi đè dữ liệu mà phiên này không tạo ra và không rõ ràng sở hữu.
- Nếu 2 tasks chạm vào cùng namespace, cache, hoặc workspace → coi là **shared-state**, phải xử lý tuần tự.

**Hành động phá hủy** (xóa, cleanup, reset, truncate, overwrite): Chỉ thực hiện khi `session_id`, `scope_id`, hoặc `resource_id` được xác định rõ ràng. Nếu không chứng minh được ownership → bỏ qua và báo cáo.

---

## 🤖 Điều phối Subagents

Xem chi tiết tại: `.agents/skills/subagent-driven-dev`

- Mỗi subagent nhận **một mục tiêu rõ ràng**, phạm vi hẹp và độc lập.
- Main Agent phải tổng hợp kết quả, giải quyết xung đột, và ra quyết định cuối.
- Mỗi instruction gửi cho subagent PHẢI bao gồm:
  - "Không tạo hoặc chỉnh sửa test files trừ khi được ủy quyền rõ ràng."
  - "Không mở rộng phạm vi ngoài mục tiêu được giao."
  - "Báo cáo kết quả và đề xuất kiểm chứng tách biệt với phần triển khai."

---

## ✅ Lập kế hoạch (Planning)

Xem chi tiết tại: `.agents/skills/writing-plans`

- Mọi tác vụ không tầm thường → **lên kế hoạch trước khi viết code**.
- Nếu yêu cầu mơ hồ → viết spec ngắn trước.
- **Kế hoạch được duyệt = ủy quyền thực thi ngay lập tức.** Không dừng lại chờ "ok" hay "tiếp tục".
- Sau khi ghi kế hoạch vào file → tiếp tục thực thi ngay trong cùng turn làm việc.

---

## ✔️ Tiêu chí Hoàn thành (Definition of Done)

Một tác vụ chỉ được coi là **HOÀN THÀNH** khi đủ tất cả:

- [ ] Phạm vi yêu cầu đã được giải quyết đầy đủ.
- [ ] Thay đổi được kiểm chứng bằng bằng chứng (evidence).
- [ ] Không có test file hoặc test code nào được tạo trái phép.
- [ ] Rủi ro và giả định quan trọng đã được nêu rõ.
- [ ] Files bị thay đổi hoặc vùng bị ảnh hưởng đã được tóm tắt.
- [ ] Không có git commit nào được tạo trừ khi người dùng yêu cầu rõ ràng.

---

## 📊 Báo cáo Cuối tác vụ (Final Report Format)

Mỗi báo cáo cuối phải bao gồm:
1. **Tóm tắt thay đổi** — Files/vùng bị ảnh hưởng.
2. **Kết quả kiểm chứng** — Lệnh đã chạy và kết quả.
3. **Trạng thái tests** — Nếu không viết test mới, ghi rõ: *"Không viết test mới vì người dùng không yêu cầu."*
4. **Giới hạn kiểm chứng** — Nếu bị hạn chế, nêu rõ mà không coi là thất bại.
5. **Đề xuất theo dõi (tùy chọn)** — Cải tiến hoặc tests có thể làm sau.

---

## 🎯 Nguyên tắc Lựa chọn (Selection Principle)

Khi có nhiều phương án hợp lệ, chọn phương án:
- Rủi ro **thấp hơn**
- Phạm vi **hẹp hơn**
- Dễ **kiểm chứng** hơn
- Dễ **review** hơn
- An toàn hơn cho **thực thi song song**
- Tuân thủ chính sách **không tự ý viết tests**
