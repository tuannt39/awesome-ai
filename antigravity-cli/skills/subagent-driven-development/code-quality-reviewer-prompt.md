# Prompt Template for Code Quality Reviewer Subagent

Use this template when dispatching a subagent to evaluate code quality.

**Purpose:** Verify if the implementation is well-built (clean, well-verified, maintainable).

**Only dispatch after the spec compliance review has reached the ✅ status.**

```
invoke_subagent (TypeName: "research" or "self"):
  Role: "Evaluate Code Quality for Task N"
  Use template at requesting-code-review/code-reviewer.md

  DESCRIPTION: [task summary, extracted from the implementer's report]
  REQUIREMENTS_OR_PLAN: Task N from [plan-file]
  BASE_SHA: [commit before task]
  HEAD_SHA: [current commit]
```

**In addition to standard code quality concerns, the reviewer should also verify:**
- Does each file have a single clear responsibility with a well-defined interface?
- Are components reasonably decomposed so they can be understood and verified independently?
- Does the implementation adhere to the file structure outlined in the plan?
- Does this implementation introduce new files that are already too large, or significantly bloat existing files? (Focus only on what this change contributes, do not flag pre-existing file sizes).

**Reviewer responds in Vietnamese including:** Strengths (Điểm mạnh), Issues (Vấn đề - Critical/Important/Minor), and Assessment (Đánh giá chung).
