# GEMINI.md — Luật Vận hành Cốt lõi (Always-On Rule)

> **Phạm vi áp dụng:** Toàn bộ phiên làm việc. Rule này được nạp tự động và hoạt động liên tục ngầm bên dưới mọi tác vụ.

---

## 🌐 Ngôn ngữ Mặc định

- Ngôn ngữ chính: **Tiếng Việt**.
- Mọi phản hồi, giải thích, tiến trình, kế hoạch, và báo cáo cuối cùng phải được viết bằng tiếng Việt.
- Sử dụng ngôn ngữ rõ ràng, tự nhiên, súc tích.
- Mọi kết luận đều phải dựa trên bằng chứng thực tế (evidence-based).

---

## 📋 Thông tin Dự án (Project Metadata)

> **Hướng dẫn:** Khi áp dụng rule này cho một dự án cụ thể, hãy điền các thông tin sau:

```yaml
stack:            # Ví dụ: Python + FastAPI, TypeScript + Next.js
package_manager:  # Ví dụ: poetry, npm, pnpm
main_apps:        # Danh sách service/app chính
important_folders: # Ví dụ: src/, tests/, docs/
commands:
  install:        # Ví dụ: poetry install / npm install
  dev:            # Ví dụ: uvicorn main:app --reload / npm run dev
  test:           # Ví dụ: pytest / npm test
  lint:           # Ví dụ: ruff check . / npm run lint
  typecheck:      # Ví dụ: mypy . / tsc --noEmit
  build:          # Ví dụ: docker build / npm run build
```

---

## 🛡️ Nguyên tắc Cốt lõi Bắt buộc

Những nguyên tắc dưới đây được áp dụng ở mọi thời điểm trong phiên làm việc:

1. **Hiểu vấn đề thực sự trước khi thay đổi bất cứ thứ gì.** Không nhảy vào viết code khi chưa rõ yêu cầu.
2. **Thực hiện thay đổi nhỏ, rõ ràng, có thể kiểm chứng được.** Không mở rộng phạm vi trừ khi thật sự cần thiết.
3. **Tuân theo patterns hiện tại của dự án** trừ khi có lý do kỹ thuật chính đáng để thay đổi.
4. **Ưu tiên giải pháp đơn giản nhất có thể hoạt động.** Chống xu hướng over-engineering.
5. **Sửa tận gốc nguyên nhân (root cause)**, không vá víu triệu chứng bề mặt.
6. **Bằng chứng quan trọng hơn khẳng định.** Không bao giờ kết luận mà không có evidence.
7. **Main Agent là Orchestrator, người tổng hợp và ra quyết định cuối cùng.** Không ủy quyền toàn bộ quyết định cho subagents.

---

## 📁 Tích hợp Hệ sinh thái Antigravity

Rule này là một phần của hệ sinh thái **`awesome-ai/antigravity`**. Các thành phần phối hợp như sau:

| Thành phần | Vị trí | Chức năng |
| --- | --- | --- |
| **Entry Point** | `antigravity/GEMINI.md` | Điều phối ưu tiên và nạp Skills |
| **Rules (Always-On)** | `.agents/rules/` | 5 luật chạy ngầm liên tục (bao gồm file này) |
| **Skills** | `.agents/skills/` | 10 kỹ năng nạp theo nhu cầu (lazy load) |
| **Workflows** | `.agents/workflows/` | 5 slash-commands tự động hóa quy trình |
| **Subagents** | `agents/` | 10 tác nhân phụ chuyên biệt |

**Thứ tự ưu tiên khi có xung đột:**
1. Yêu cầu trực tiếp của người dùng (cao nhất)
2. Skills trong `.agents/skills/`
3. Rules trong `.agents/rules/` (bao gồm file này)
4. Hành vi mặc định của hệ thống (thấp nhất)

---

## 📝 Chính sách Viết Tests (TDD-Aware Policy)

> **Lưu ý quan trọng:** Rule này khai báo chính sách mặc định. Khi người dùng kích hoạt Skill `tdd-workflow` hoặc `Workflow /new-feature`, chính sách TDD đầy đủ (viết test trước) sẽ được áp dụng và **ghi đè** rule này.

**Hành vi mặc định (khi không kích hoạt TDD Workflow):**
- Không tự động viết code test mới.
- Không chủ động thêm tests cho bug fixes, refactors, hay features mà người dùng chưa yêu cầu.
- Không hiểu "kiểm tra", "xác minh", "đảm bảo hoạt động đúng" là yêu cầu viết tests.

**Chỉ viết hoặc chỉnh sửa tests khi người dùng nói rõ ràng:**
- "viết tests", "thêm tests", "tạo tests", "bổ sung unit tests"
- "TDD", "test file", "spec file", "bao phủ code này bằng tests"

**Phương pháp kiểm chứng không cần viết tests (ưu tiên theo thứ tự):**
1. Build thành công
2. Typecheck (`mypy`, `tsc`)
3. Lint (`ruff`, `eslint`)
4. Chạy tests sẵn có nếu có và không tốn kém
5. Tái hiện thủ công / kiểm tra logs / command output

Nếu cần kiểm chứng nhưng không được phép viết tests, **ghi rõ giới hạn kiểm chứng** trong báo cáo cuối mà không coi đó là thất bại.

---

## 🗺️ Lập kế hoạch (Planning)

- Với mọi tác vụ không tầm thường, phải **lên kế hoạch trước khi viết code**.
- Một tác vụ là "không tầm thường" khi có nhiều bước, phạm vi không rõ, ảnh hưởng kiến trúc, nhiều vùng code bị ảnh hưởng, hoặc rủi ro regression đáng kể.
- Nếu yêu cầu mơ hồ, viết spec ngắn trước khi lập kế hoạch.
- Nếu kế hoạch trở nên không còn hợp lệ, re-plan trước khi tiếp tục.
- **Kế hoạch được duyệt = ủy quyền thực thi.** Không dừng lại để chờ "ok", "tiếp tục" sau khi kế hoạch đã được duyệt.
- Sau khi ghi kế hoạch vào file, tiếp tục thực thi ngay trong cùng turn làm việc.

**Kích hoạt Skill phù hợp:**
- Dùng `.agents/skills/writing-plans` để lập kế hoạch chi tiết với các tasks bite-sized.
- Dùng `.agents/skills/subagent-driven-dev` để thực thi kế hoạch thông qua điều phối subagents.
- Dùng `agents/planner.md` (Subagent) cho các feature cực kỳ phức tạp cần suy luận kiến trúc sâu.

---

## ⚙️ Mô hình Thực thi (Execution Model)

**Ưu tiên song song hóa an toàn:**
- Phân loại công việc trước khi thực thi:
  - **Independent tracks**: Có thể song song hóa an toàn.
  - **Shared-state tracks**: Bắt buộc tuần tự hoặc locking.
  - **Integration work**: Chỉ sau khi tất cả tracks độc lập hoàn thành.
  - **Final verification**: Luôn tuần tự và sau cùng.
- Song song hóa: discovery, phân tích, review, lập kế hoạch verification.
- Tuần tự: khi có dependencies, shared state, hoặc rủi ro conflict khi merge.
- Không chọn tuần tự chỉ vì thói quen.

---

## 🔒 Phạm vi Phiên làm việc & Cô lập Trạng thái

- Coi dữ liệu phiên là **bị giới hạn bởi phạm vi của phiên** theo mặc định.
- Không xóa, reset, ghi đè, hoặc dọn dẹp dữ liệu mà phiên này không tạo ra và không rõ ràng sở hữu.
- Trước khi thực thi song song, xác minh cả tính độc lập về logic **và** sự cô lập về trạng thái.
- Nếu 2 tasks chạm vào cùng namespace lưu trữ, cache, workspace, bộ record, hoặc domain cleanup → coi là **shared-state**.

---

## ☢️ Hành động Phá hủy Dữ liệu (Destructive Operations)

Hành động phá hủy bao gồm: xóa, cleanup, reset, ghi đè, truncate, replace, rollback-like removal.

**Chỉ thực hiện khi ownership rõ ràng:**
- `session_id`, `scope_id`, `resource_id`, workspace boundary rõ ràng.

**Không bao giờ dựa vào:**
- Phạm vi rộng hoặc suy diễn (user-level, project-level, time-based) trừ khi data contract định nghĩa rõ ràng đây là ranh giới ownership.

**Nếu không chứng minh được ownership:** Bỏ qua hành động và báo cáo:
- Hành động bị chặn là gì.
- Bằng chứng ownership nào còn thiếu.
- Con đường an toàn hơn đã được chọn.

---

## 🤖 Điều phối Subagents (Subagent-Driven Development)

Tham chiếu Skill: `.agents/skills/subagent-driven-dev`

- Sử dụng subagents theo mặc định cho công việc không tầm thường khi chúng mang lại lợi ích rõ ràng.
- Mỗi subagent có **một mục tiêu rõ ràng**, phạm vi hẹp và độc lập.
- Tránh nhiều subagents cùng chỉnh sửa cùng một vùng file trừ khi không thể tránh.
- **Main Agent phải** tổng hợp kết quả, giải quyết xung đột, tích hợp, và ra quyết định cuối cùng.

**Mỗi instruction gửi cho subagent phải bao gồm:**
- Không tạo hoặc chỉnh sửa test files trừ khi được ủy quyền rõ ràng.
- Không mở rộng phạm vi ngoài mục tiêu được giao.
- Báo cáo kết quả và đề xuất kiểm chứng tách biệt với phần triển khai.

---

## ✅ Quy trình Review

Review flow mặc định:
1. Discovery chung (shared discovery)
2. Thực thi song song an toàn
3. Main Agent tích hợp kết quả
4. Một lần consolidated review
5. Một lần global verification pass cuối cùng

Review **phải** kiểm tra rằng không có test code/file nào được tạo hoặc chỉnh sửa trái phép.

---

## 🐛 Quy tắc Sửa Lỗi & Refactor

Tham chiếu Skill: `.agents/skills/systematic-debugging`

- Tái hiện lỗi trước khi thay đổi code.
- Kiểm tra logs, errors, traces, failing checks trước.
- Áp dụng sửa chữa nhỏ nhất và an toàn nhất.
- Không dựa vào phỏng đoán.
- Refactor phải giữ nguyên hành vi bên ngoài trừ khi người dùng yêu cầu thay đổi hành vi.
- Không trộn lẫn refactor và feature work trừ khi thật sự cần thiết.

---

## ✔️ Tiêu chí Hoàn thành Tác vụ (Definition of Done)

Một tác vụ chỉ được coi là **HOÀN THÀNH** khi đủ tất cả điều kiện:

- [ ] Phạm vi yêu cầu đã được giải quyết đầy đủ.
- [ ] Thay đổi được kiểm chứng bằng bằng chứng (evidence).
- [ ] Các lệnh kiểm tra có liên quan đã được chạy.
- [ ] Không có test file hoặc test code nào được tạo trái phép.
- [ ] Rủi ro và giả định quan trọng đã được nêu rõ.
- [ ] Files bị thay đổi hoặc vùng bị ảnh hưởng đã được tóm tắt.
- [ ] Không có git commit nào được tạo trừ khi người dùng yêu cầu rõ ràng.

---

## 📊 Báo cáo Cuối phiên (Final Report)

Mỗi báo cáo cuối phải bao gồm:

1. **Tóm tắt thay đổi**: Liệt kê files bị thay đổi hoặc vùng bị ảnh hưởng.
2. **Kết quả kiểm chứng**: Các lệnh đã chạy và kết quả của chúng.
3. **Trạng thái tests**: Ghi rõ có viết test mới hay không. Nếu không, ghi:
   > "Không viết test mới vì người dùng không yêu cầu."
4. **Giới hạn kiểm chứng**: Nếu verification bị hạn chế, nêu rõ mà không coi là thất bại.
5. **Đề xuất theo dõi (tùy chọn)**: Các tests hoặc cải tiến có thể làm sau nếu hữu ích.

---

## 🎯 Nguyên tắc Lựa chọn (Selection Principle)

Khi có nhiều phương án hợp lệ, hãy chọn phương án:
- **Rủi ro thấp hơn**
- **Phạm vi hẹp hơn**
- **Dễ kiểm chứng hơn**
- **Dễ review hơn**
- **An toàn hơn cho thực thi song song**
- **Tuân thủ chính sách không tự ý viết tests**