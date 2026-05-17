---
name: subagent-driven-dev
description: Kích hoạt khi thực thi implementation plans với các tasks độc lập trong session hiện tại. Phái subagent mới cho mỗi task, với 2-stage review (spec compliance rồi code quality).
origin: superpowers
---

# Subagent-Driven Development — Phát triển Hướng Tác nhân Phụ

Thực thi plan bằng cách phái subagent mới cho mỗi task, với 2-stage review sau mỗi task: spec compliance review trước, sau đó code quality review.

**Tại sao dùng subagents:** Bạn ủy quyền tasks cho specialized agents với isolated context. Bằng cách craft instructions và context chính xác, bạn đảm bảo họ tập trung và thành công. Họ không bao giờ inherit session context của bạn — bạn construct chính xác những gì họ cần.

**Core principle:** Fresh subagent per task + 2-stage review (spec rồi quality) = high quality, fast iteration

## Continuous Execution

Không dừng để check in với người dùng giữa các tasks. Thực thi tất cả tasks từ plan mà không dừng. Lý do duy nhất để dừng: BLOCKED status bạn không thể resolve, ambiguity ngăn progress thực sự, hoặc tất cả tasks hoàn thành.

## The Process

### Preparation
1. Đọc plan file một lần, extract tất cả tasks với full text và context
2. Tạo TodoWrite với tất cả tasks
3. Bắt đầu thực thi theo thứ tự

### Per Task Loop

```
[Dispatch implementer subagent với full task text + context]
    ↓
Subagent hỏi questions?
    ├── Có → Answer questions, provide context → Re-dispatch
    └── Không → Subagent implements, tests, commits, self-reviews
                    ↓
            [Dispatch spec compliance reviewer]
                    ↓
            Spec compliant?
                ├── Không → Implementer fixes gaps → Re-review
                └── Có → [Dispatch code quality reviewer]
                                ↓
                        Code quality approved?
                            ├── Không → Implementer fixes → Re-review
                            └── Có → Mark task complete
                                            ↓
                                    More tasks remain?
                                        ├── Có → Dispatch next implementer
                                        └── Không → [Dispatch final code reviewer]
                                                            ↓
                                                    Kích hoạt finishing-branch skill
```

## Model Selection — Tối ưu Chi phí

| Loại Task | Model |
| --- | --- |
| Mechanical implementation (isolated functions, clear specs, 1-2 files) | Model nhỏ/nhanh |
| Integration và judgment tasks (multi-file, pattern matching) | Model standard |
| Architecture, design, và review tasks | Model mạnh nhất |

## Handling Implementer Status

| Status | Hành động |
| --- | --- |
| **DONE** | Tiến đến spec compliance review |
| **DONE_WITH_CONCERNS** | Đọc concerns trước. Nếu correctness/scope → Address. Nếu observations → Note và tiếp tục |
| **NEEDS_CONTEXT** | Cung cấp missing context và re-dispatch |
| **BLOCKED** | Assess blocker: context problem → provide more; cần thêm reasoning → upgrade model; task quá lớn → break nhỏ hơn; plan sai → escalate to human |

**Không bao giờ** ignore escalation hoặc force cùng model retry mà không có thay đổi.

## Red Flags

**Không bao giờ:**
- Bắt đầu implementation trên main/master branch mà không có sự đồng ý
- Bỏ qua reviews (spec compliance HOẶC code quality)
- Tiến tới với unfixed issues
- Phái nhiều implementation subagents song song (conflicts)
- Cho subagent đọc plan file (cung cấp full text thay thế)
- Cho spec reviewer làm trước code quality reviewer
- Chuyển sang task tiếp theo khi vẫn còn open issues

## Integration với Skills Khác

**Required workflow skills:**
- **brainstorming** — Thiết kế trước khi execute
- **writing-plans** — Tạo plan mà skill này execute
- **tdd-workflow** — Subagents follow TDD cho mỗi task
- **finishing-branch** — Hoàn thành sau khi tất cả tasks xong
