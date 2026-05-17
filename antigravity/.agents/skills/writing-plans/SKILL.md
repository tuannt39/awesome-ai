---
name: writing-plans
description: Kích hoạt khi có spec hoặc requirements cho một tác vụ nhiều bước, TRƯỚC khi chạm vào code.
origin: superpowers
---

# Writing Plans — Lập Kế hoạch Triển khai

**Announce khi bắt đầu:** "Tôi đang dùng writing-plans skill để tạo implementation plan."

## Tổng quan

Viết implementation plans toàn diện với giả định engineer có zero context về codebase. Document mọi thứ họ cần biết: files nào cần touch cho mỗi task, code, testing, docs cần check, cách test. DRY. YAGNI. TDD. Frequent commits.

## Plan Document Header

**Mỗi plan PHẢI bắt đầu với header này:**

```markdown
# [Feature Name] Implementation Plan

> **Cho agentic workers:** SUB-SKILL BẮT BUỘC: Dùng subagent-driven-dev (recommended) hoặc executing-plans để implement plan này từng task một. Steps dùng checkbox (- [ ]) syntax để tracking.

**Goal:** [Một câu mô tả cái gì được build]

**Architecture:** [2-3 câu về approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## File Structure — Trước khi Define Tasks

Trước khi định nghĩa tasks, map out file nào sẽ được tạo hoặc sửa đổi:

- Design units với clear boundaries và well-defined interfaces
- Mỗi file có một trách nhiệm rõ ràng
- Files thay đổi cùng nhau nên sống cùng nhau
- Split theo responsibility, không phải theo technical layer

## Bite-Sized Task Granularity

**Mỗi bước là một hành động (2-5 phút):**
- "Viết failing test" — một bước
- "Chạy để đảm bảo nó fail" — một bước
- "Implement minimal code để test pass" — một bước
- "Chạy tests và đảm bảo pass" — một bước
- "Commit" — một bước

## Task Structure Template

```markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

- [ ] **Step 1: Viết failing test**

  ```python
  def test_specific_behavior():
      result = function(input)
      assert result == expected
  ```

- [ ] **Step 2: Chạy test để verify nó fail**

  Run: `pytest tests/path/test.py::test_name -v`
  Expected: FAIL with "function not defined"

- [ ] **Step 3: Viết minimal implementation**

  ```python
  def function(input):
      return expected
  ```

- [ ] **Step 4: Chạy test để verify nó pass**

  Run: `pytest tests/path/test.py::test_name -v`
  Expected: PASS

- [ ] **Step 5: Commit**

  ```bash
  git add tests/path/test.py src/path/file.py
  git commit -m "feat: add specific feature"
  ```
```

## Không Có Placeholders

Mọi bước phải chứa nội dung thực tế engineer cần. Đây là **plan failures** — không bao giờ viết:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (không có actual test code)
- "Similar to Task N" (repeat the code — engineer có thể đọc tasks không theo thứ tự)
- References đến types, functions không được định nghĩa trong bất kỳ task nào

## Lưu Plan

Lưu tại: `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`

## Self-Review sau khi viết Plan

1. **Spec coverage:** Scan từng section/requirement trong spec. Có task implement không?
2. **Placeholder scan:** Search cho red flags như "TBD", "TODO".
3. **Type consistency:** Types, method signatures nhất quán giữa các tasks không?

## Execution Handoff

Sau khi lưu plan, offer execution choice:

> "Plan hoàn thành và lưu tại `docs/plans/<filename>.md`. Hai tùy chọn thực thi:
>
> **1. Subagent-Driven (recommended)** — Phái subagent mới cho mỗi task, review giữa các tasks
>
> **2. Inline Execution** — Thực thi tasks trong session này dùng executing-plans
>
> Bạn chọn cách nào?"

- Nếu chọn Subagent-Driven → Kích hoạt **subagent-driven-dev** skill
- Nếu chọn Inline → Thực thi từng task theo plan với checkpoints
