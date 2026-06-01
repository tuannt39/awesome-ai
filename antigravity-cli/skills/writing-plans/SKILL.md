---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive execution plans in **Vietnamese** assuming that the implementing engineer has little initial project context. The plan must specify: files to edit/create, sample source code, verification methods, necessary reference documentation, and break down the work into reasonable, easy-to-track tasks. Strictly follow DRY and YAGNI principles.

**Announcement at startup:** "Tôi đang sử dụng skill writing-plans để tạo kế hoạch thực thi."

**Plan Save Location:** The project's plan directory: `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`
- (Note: Any specific user configuration or requirements regarding the save location will take the highest priority).

## Strict Test Policy
* **Absolutely do not write new tests unless explicitly requested by the user (such as "write unit tests", "TDD", "add unit tests").**
* The plan must explicitly state the following sentence in the Header:
  
  *"Không viết test mới; chỉ dùng kiểm chứng không tạo test hoặc chạy test sẵn có nếu phù hợp."*

* Instead of writing new tests for verification, prioritize alternative methods: trial compilation (build), static typecheck, linting, observing logs, or utilizing existing test suites if convenient.

## Scope Check

If the design Spec document covers too many independent subsystems, it needs to be split into multiple smaller plans (one plan per subsystem). Each plan must produce a working and independently verifiable version of the software.

## Task Breakdown & Structure of Files

Before writing specific steps, map out the diagram of files to be changed or created, along with their specific roles. This helps implementing subagents grasp the overall architecture.

**Manageable Task Sizes:**
Each execution step should take between 2 to 5 minutes, focusing on a single small action and allowing immediate outcome verification.

## Header Structure of a Sample Plan

**Each plan must start with the following Header structure:**

```markdown
# Execution Plan: [Feature Name]

> **For Agent:** Required to use the `invoke_subagent` tool to coordinate subagents to implement this plan task-by-task. Use the artifact file `task.md` at `<artifactDir>/task.md` to track and update statuses (`- [ ]`).

**Goal:** [A single-sentence description of the goal of this plan]

**Architecture & Solution:** [Brief description of the approach]

**Technology & Tools:** [Tools, libraries, or commands that will be used in Antigravity CLI]

**Test Policy:** Không viết test mới; chỉ dùng kiểm chứng không tạo test hoặc chạy test sẵn có nếu phù hợp.

---
```

## Detailed Task Structure

```markdown
### Task N: [Component/Module Name]

**Affected Files:**
- Create new: `path/to/exact/file.py`
- Modify: `path/to/exact/existing.py:123-145`
- Verification method: Type checking and running the actual application to observe logs.

- [ ] **Step 1: [Describe specific action in Vietnamese]**
  
  Source code details to add/modify:
  ```python
  # Specific sample source code here
  def business_logic():
      pass
  ```

- [ ] **Step 2: [Describe specific verification step in Vietnamese]**
  
  Verification run command: `python path/to/script.py` or `npm run build`
  Expected result: Successful compilation, no runtime errors.
```

## Say No to Placeholders

Each execution step must provide sufficient technical information for the engineer/subagent. Absolutely do not use ambiguous phrases like:
- "TBD", "TODO", "to be installed later", "fill details here".
- "Add appropriate error handling" (without providing a sample code of how to handle errors).
- "Write test for the section above" (without providing specific test code or verification methods).
- Code blocks must be fully written out, clear, and not truncated.

## Plan Self-Review Workflow

After drafting the plan, you must perform a self-review based on these criteria:
1. **Spec Coverage:** Has every requirement in the Spec been translated into at least one task in the Plan?
2. **Scan for Placeholders:** Are there any placeholder keywords or vague descriptions? Correct them directly.
3. **Consistency in Typing and Naming:** Ensure functions, variables, and classes are named consistently across all tasks.

## Gate 2: Approve Plan (Hard Gate 2)
After saving the Plan file and self-reviewing, you MUST output the exact pause message before moving to the execution phase:
> "⏸️ **Chờ duyệt Plan** — Gõ OK hoặc Duyệt để tiếp tục."

and **IMMEDIATELY STOP ALL TOOL CALLS**. Wait for the user's response. When the user approves (types "OK" or "Duyệt"), you then automatically activate `subagent-driven-development` to begin executing the plan using subagents.
