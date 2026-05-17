---
name: bugfix
command: /bugfix
description: Quy trình sửa lỗi hệ thống. Điều phối chẩn đoán có hệ thống và sửa lỗi theo chuẩn TDD để tránh regression.
---

# Workflow: /bugfix — Sửa lỗi Hệ thống

Quy trình tự động hóa để gỡ lỗi và sửa lỗi một cách có hệ thống, đảm bảo không tạo ra lỗi mới.

## Các Bước Điều phối (Orchestration Steps)

### Step 1: Systematic Investigation
- **Kích hoạt Skill:** `skills/systematic-debugging`
- **Hành động:**
  1. Điều tra triệu chứng lỗi, đọc stack traces và logs.
  2. Thu thập bằng chứng trong hệ thống (evidence gathering).
  3. Hình thành giả thuyết duy nhất (single hypothesis) về nguyên nhân gốc rễ (root cause).
- **Gate:** Xác định được nguyên nhân gốc rễ và cơ chế tái hiện trước khi sửa code.

### Step 2: Test-Driven Fix (TDD)
- **Kích hoạt Skill:** `skills/tdd-workflow`
- **Hành động:**
  1. Viết một automated test case tái hiện chính xác lỗi (RED).
  2. Chạy test và xác nhận test thất bại vì lý do lỗi này.
  3. Viết code sửa lỗi tối thiểu để test vượt qua (GREEN).
  4. Xác nhận test đã pass và refactor code nếu cần.

### Step 3: Local Verification
- **Kích hoạt Skill:** `skills/verification-loop`
- **Hành động:** Chạy lại toàn bộ test suite của dự án để đảm bảo lỗi được sửa triệt để và không gây ảnh hưởng phụ (no regression).

### Step 4: Commit & Branch Finish
- **Kích hoạt Skill:** `skills/finishing-branch`
- **Hành động:** Commit với conventional commit `fix(...)` và trình bày các bước hoàn tất nhánh.
