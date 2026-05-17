---
name: skill-creator
description: Hướng dẫn để tạo và đóng gói skills mới. Kích hoạt khi người dùng muốn tạo một skill mới để mở rộng khả năng của hệ thống với knowledge, workflows, hoặc tool integrations đặc thù.
origin: awesome-claude-skills
---

# Skill Creator — Trình Tạo Kỹ năng

Kỹ năng này cung cấp hướng dẫn và quy chuẩn để tạo ra các skills hiệu quả cho hệ sinh thái Antigravity.

## Về Skills trong Antigravity

Skills là các gói module độc lập giúp mở rộng khả năng của Agent bằng cách cung cấp knowledge chuyên biệt, workflows, và công cụ.

### Giải phẫu một Skill

Mỗi skill bao gồm file `SKILL.md` bắt buộc và các tài nguyên đi kèm (tùy chọn):

```
skill-name/
├── SKILL.md (bắt buộc)
│   ├── YAML frontmatter metadata (bắt buộc)
│   │   ├── name: (bắt buộc)
│   │   ├── description: (bắt buộc)
│   │   └── origin: (tùy chọn)
│   └── Markdown instructions (bắt buộc)
└── Bundled Resources (tùy chọn)
    ├── scripts/          - Code thực thi (Python/Bash)
    ├── references/       - Tài liệu tham khảo (schema, policy)
    └── assets/           - Files dùng cho output (templates, boilerplate)
```

## Quy trình Tạo Skill

### Step 1: Hiểu Skill qua Ví dụ Cụ thể
- Hỏi người dùng về các use-cases cụ thể của skill.
- "Người dùng sẽ nói gì để trigger skill này?"
- Đừng hỏi quá nhiều câu cùng lúc.

### Step 2: Lập kế hoạch Nội dung Tái sử dụng
Phân tích xem skill cần:
- `scripts/`: Có code nào cần thực thi lặp lại không?
- `references/`: Có documentation, schema nào cần tham khảo không?
- `assets/`: Có template HTML/React hay boilerplate nào cần dùng không?

### Step 3: Tạo SKILL.md
Tạo file `SKILL.md` với định dạng sau:

```markdown
---
name: [tên-skill-ngắn-gọn]
description: [Mô tả rõ ràng KHI NÀO nên trigger skill này, ở ngôi thứ 3]
---

# Tên Skill

[Mục đích của skill trong 1-2 câu]

## Khi nào Sử dụng
- [Trường hợp 1]
- [Trường hợp 2]

## Quy trình / Hướng dẫn
[Viết bằng thể mệnh lệnh (imperative), ví dụ: "Thực hiện X", "Kiểm tra Y"]
```

**Nguyên tắc viết:**
- Sử dụng thể mệnh lệnh/hướng dẫn khách quan (verb-first).
- "Để làm X, hãy thực thi Y" thay vì "Bạn nên làm X".
- Tránh dùng ngôi thứ hai ("You").

### Step 4: Nguyên tắc Thiết kế Progressive Disclosure (Tiết lộ Lũy tiến)

Skills trong Antigravity quản lý context theo 3 cấp độ:
1. **Metadata (name + description):** Luôn nằm trong system prompt (rất ngắn).
2. **SKILL.md body:** Chỉ được đọc khi skill được kích hoạt.
3. **Bundled resources:** Chỉ đọc khi agent quyết định cần tham khảo (thông qua tool).

**Lưu ý quan trọng:** KHÔNG nhồi nhét quá nhiều thông tin vào `SKILL.md`. Nếu có tài liệu dài, hãy để trong thư mục `references/` và hướng dẫn trong `SKILL.md` cách đọc file đó.

### Step 5: Lưu và Review
- Tạo thư mục `.agents/skills/<skill-name>/`
- Ghi file `SKILL.md` và các resources.
- Xác nhận lại với người dùng để họ có thể test và iterate.
