# Antigravity CLI Tool Mapping

Khi skills tham chiếu tool names từ Claude Code / Copilot CLI / Gemini CLI,
dùng bảng mapping sau cho Antigravity CLI.

## Tool Mapping

| Superpowers / Claude Code | Antigravity CLI | Ghi chú |
|---------------------------|-----------------|----------|
| `Skill` tool | `view_file` trên SKILL.md | Đọc `IsSkillFile: true` |
| `TodoWrite` | Artifact `task.md` | File `<artifactDir>/task.md` |
| `Task` (dispatch subagent) | `invoke_subagent` | Kèm `define_subagent` nếu cần |
| `Task` (general-purpose) | `invoke_subagent` với TypeName `self` | Hoặc define custom subagent |
| `EnterWorktree` | `invoke_subagent` với `Workspace: "branch"` | Tạo isolated workspace |
| `Read` | `view_file` | |
| `Write` / `Edit` | `write_to_file` / `replace_file_content` / `multi_replace_file_content` | |
| `Bash` / `Shell` | `run_command` | |
| `WebSearch` | `search_web` | |
| `WebFetch` | `read_url_content` | |

## Subagent Dispatch

Thay vì `Task tool (general-purpose)`, dùng pattern:

```
invoke_subagent:
  TypeName: "self" hoặc custom defined agent
  Role: "[mô tả vai trò]"
  Prompt: |
    [nội dung prompt template]
```

## Workspace Isolation

| Superpowers | Antigravity CLI |
|-------------|------------------|
| `git worktree add` | `Workspace: "branch"` trong invoke_subagent |
| Normal repo | `Workspace: "inherit"` (mặc định) |
| Shared workspace | `Workspace: "share"` |

## Artifact vs TodoWrite

Thay vì `TodoWrite`, tạo/cập nhật artifact file:
- Path: `<artifactDir>/task.md`
- Format: `- [ ]` / `- [/]` / `- [x]`
- Dùng `write_to_file` hoặc `replace_file_content` để cập nhật

## Policy Notes (GEMINI.md)

- **Không git commit tự động** trừ khi user yêu cầu
- **Không viết test** trừ khi user yêu cầu ("write tests", "TDD", "add unit tests")
- **Không tạo git worktree** trừ khi user yêu cầu — dùng `Workspace: "branch"` thay thế
- **Auto-transition** sang subagent-driven-development sau khi plan approved
