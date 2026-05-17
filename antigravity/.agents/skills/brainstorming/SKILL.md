---
name: brainstorming
description: "Kích hoạt trước mọi công việc sáng tạo - tạo tính năng mới, xây dựng components, thêm chức năng, hoặc thay đổi hành vi. Khám phá ý định, yêu cầu và thiết kế TRƯỚC khi triển khai."
origin: superpowers
---

# Brainstorming — Biến Ý tưởng thành Thiết kế

Giúp biến ý tưởng thành thiết kế và spec hoàn chỉnh thông qua hội thoại hợp tác tự nhiên.

Bắt đầu bằng cách hiểu bối cảnh dự án hiện tại, sau đó hỏi từng câu hỏi một để làm rõ ý tưởng. Khi đã hiểu mình đang xây dựng gì, trình bày thiết kế và nhận sự chấp thuận từ người dùng.

<HARD-GATE>
KHÔNG kích hoạt bất kỳ implementation skill nào, không viết code, scaffold project, hoặc thực hiện bất kỳ hành động triển khai nào cho đến khi đã trình bày thiết kế VÀ người dùng đã chấp thuận. Điều này áp dụng cho MỌI dự án bất kể độ phức tạp.
</HARD-GATE>

## Checklist Bắt buộc

Phải tạo task cho từng mục và hoàn thành theo thứ tự:

1. **Khám phá bối cảnh dự án** — kiểm tra files, docs, recent commits
2. **Hỏi câu hỏi làm rõ** — từng câu một, hiểu mục đích/ràng buộc/tiêu chí thành công
3. **Đề xuất 2-3 cách tiếp cận** — với trade-offs và khuyến nghị
4. **Trình bày thiết kế** — theo từng phần theo độ phức tạp, nhận chấp thuận sau mỗi phần
5. **Viết design doc** — lưu tại `docs/specs/YYYY-MM-DD-<topic>-design.md` và commit
6. **Spec self-review** — kiểm tra nhanh placeholders, mâu thuẫn, ambiguity, scope
7. **Người dùng review spec** — yêu cầu review trước khi tiếp tục
8. **Chuyển sang implementation** — kích hoạt writing-plans skill để tạo implementation plan

## Process Flow

```
Khám phá bối cảnh
    ↓
Hỏi câu hỏi làm rõ (từng câu)
    ↓
Đề xuất 2-3 cách tiếp cận
    ↓
Trình bày thiết kế (từng phần)
    ↓
Người dùng chấp thuận?
    ├── Không → Revise và trình bày lại
    └── Có → Viết design doc
              ↓
          Spec self-review (fix inline)
              ↓
          Người dùng review spec
              ↓
          Kích hoạt writing-plans skill
```

**Terminal state là kích hoạt writing-plans.** Skill duy nhất kích hoạt sau brainstorming là writing-plans.

## Nguyên tắc Hỏi

- **Một câu hỏi mỗi lần** — Không overwhelm với nhiều câu cùng lúc
- **Multiple choice ưu tiên** — Dễ trả lời hơn open-ended khi có thể
- **YAGNI ngay từ đầu** — Loại bỏ features không cần thiết khỏi thiết kế
- **Explore alternatives** — Luôn đề xuất 2-3 cách trước khi chốt

## Trình bày Thiết kế

- Trình bày theo từng phần theo độ phức tạp của chúng
- Hỏi sau mỗi phần: "Phần này có ổn không?"
- Cover: kiến trúc, components, data flow, error handling, testing
- Sẵn sàng quay lại và làm rõ nếu có gì không hợp lý

## Thiết kế cho Isolation và Clarity

- Chia hệ thống thành các đơn vị nhỏ mỗi đơn vị có một mục đích rõ ràng
- Mỗi đơn vị có thể hiểu và test độc lập
- Câu hỏi test: "Có thể hiểu đơn vị này làm gì mà không đọc internals không?"

## Sau Thiết kế

**Documentation:**
- Viết validated design (spec) vào `docs/specs/YYYY-MM-DD-<topic>-design.md`
- Commit design document vào git

**Spec Self-Review:**
1. **Placeholder scan:** Bất kỳ "TBD", "TODO", incomplete sections? Fix chúng.
2. **Internal consistency:** Các sections có mâu thuẫn nhau không?
3. **Scope check:** Đủ tập trung cho một implementation plan không?
4. **Ambiguity check:** Bất kỳ requirement nào có thể hiểu theo 2 cách không?

**User Review Gate:**
> "Spec đã được viết và commit vào `<path>`. Vui lòng review và cho biết nếu muốn thay đổi gì trước khi bắt đầu viết implementation plan."

**Implementation:**
- Kích hoạt writing-plans skill để tạo implementation plan chi tiết
- KHÔNG kích hoạt bất kỳ skill nào khác. writing-plans là bước tiếp theo.
