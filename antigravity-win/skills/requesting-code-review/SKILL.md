---
name: requesting-code-review
description: Use when work is completed, important features are implemented, or before merging to verify product quality.
---

# Requesting Code Review

Trigger a reviewer subagent to catch technical errors before they cause cascading consequences. The reviewer subagent will receive a precisely set up context for the evaluation — absolutely do not share your entire session history. This helps the reviewer focus maximally on the source code, avoiding noise from your thought process, while preserving your working context to continue with other tasks.

**Core Principle:** Review early, review often.

## When to Request a Code Review

**Mandatory:**
- After completing each task in the subagent-driven development workflow.
- After completing a major feature of the project.
- Before merging the development branch into the main branch (main/master).

**Optional but extremely useful:**
- When you are stuck (need a fresh, independent perspective).
- Before performing major codebase refactoring (baseline check).
- After fixing a complex bug.

## How to Request

**1. Retrieve the Git SHA of the changes:**
```powershell
# On Windows PowerShell, get the SHA of the previous commit or origin/main as base
$BASE_SHA = git rev-parse HEAD~1
$HEAD_SHA = git rev-parse HEAD
```

**2. Trigger the reviewer subagent:**

Use the Antigravity CLI subagent definition and invocation mechanism (`invoke_subagent` with `TypeName` as `research` or `self`), using the template at `C:\Users\tuannt21\.gemini\antigravity-cli\skills\requesting-code-review\code-reviewer.md`.

**Fields to fill:**
- `{DESCRIPTION}` - A brief summary of what you built or changed.
- `{PLAN_OR_REQUIREMENTS}` - The technical requirements the code must meet.
- `{BASE_SHA}` - The starting commit (base commit).
- `{HEAD_SHA}` - The ending commit (head commit containing the latest changes).

**3. Process the reviewer feedback:**
- Fix Critical issues immediately.
- Fix Important issues before moving to the next task.
- Note Minor issues for future optimization.
- Politely counter-argue if the reviewer's assessment is incorrect (with clear reasoning).

## Real-world Example

```
[Just completed Task 2: Add index verification functionality]

You: Let me request a code review before moving to the next step.

# Run PowerShell command to get SHAs
$BASE_SHA = git log --oneline | Select-String "Task 1" | Select-Object -First 1 | ForEach-Object { $_.Line.Split(' ')[0] }
$HEAD_SHA = git rev-parse HEAD

[Trigger reviewer subagent via Antigravity CLI]
  DESCRIPTION: Added verifyIndex() and repairIndex() functions supporting 4 common error types.
  PLAN_OR_REQUIREMENTS: Task 2 in the plan file C:\Users\tuannt21\.gemini\antigravity-cli\brain\plans\deployment-plan.md
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661

[Subagent responds]:
  Strengths: Clear architecture, good real-world verification.
  Issues found:
    Important: Missing execution progress indicators.
    Minor: Magic number (100) used for reporting interval.
  Overall Assessment: Ready to proceed.

You: [Proceed to add progress indicators to fix the Important issue]
[Then confidently move on to Task 3]
```

## Integration into the Workflow

**Subagent-driven Development Process:**
- Review after EVERY task.
- Catch technical errors early before they accumulate and become complex.
- Fix issues thoroughly before starting a new task.

**Executing Large Plans:**
- Review after each task or at natural verification checkpoints.
- Receive feedback, apply modifications, then continue.

## Red Flags

**ABSOLUTELY DO NOT:**
- Skip the review step because you think "this task is too simple."
- Ignore Critical warnings.
- Continue implementing new tasks when Important issues remain unresolved.
- Argue without basis against accurate technical feedback.

See the detailed configuration template at: `C:\Users\tuannt21\.gemini\antigravity-cli\skills\requesting-code-review\code-reviewer.md`
