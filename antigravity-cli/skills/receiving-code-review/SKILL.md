---
name: receiving-code-review
description: Use when receiving code review feedback, before implementing suggested changes, especially when feedback is unclear or technically problematic.
---

# Code Review Reception

## Overview

Code review requires rigorous technical evaluation, not a social emotional performance.

**Core Principle:** Verify before executing. Clarify before assuming. Ensure technical correctness rises above social politeness.

## The Response Pattern

```
WHEN receiving code review feedback:

1. READ: Read the entire feedback without reacting immediately
2. UNDERSTAND: Restate the request in your own words (or ask if unclear)
3. VERIFY: Cross-reference with the reality of the current codebase
4. EVALUATE: Is this proposal technically logical for THIS codebase?
5. RESPOND: Confirm technically or offer a well-reasoned counter-argument
6. IMPLEMENT: Address items one by one, verifying carefully after each change
```

## Banned Responses

**ABSOLUTELY DO NOT say:**
- "You are completely right!" (a clear violation of automatically agreeing without verification)
- "Great idea!" / "Excellent feedback!" (empty, social platitudes)
- "Let me fix it right now" (prior to technical verification)

**INSTEAD:**
- Clear restate the technical requirement.
- Ask questions to clarify ambiguous points.
- Counter-argue with clear technical logic and evidence if the suggestion is incorrect.
- Get straight to implementation (action speaks louder than empty social words).

## Handling Ambiguous Feedback

```
IF any item is unclear:
  STOP - do not implement any changes yet
  ASK your partner to clarify those unclear items

REASON: Items may be closely coupled. Partial understanding leads to incorrect implementation.
```

**Example:**
```
Partner: "Please fix items 1 through 6"
You understand items 1, 2, 3, 6. Unclear on items 4 and 5.

❌ WRONG: Fix items 1, 2, 3, 6 now, then ask about 4 and 5 later.
✅ RIGHT: "I clearly understand items 1, 2, 3, 6. Please clarify items 4 and 5 before I begin implementation."
```

## Handling by Feedback Source

### From Your Partner (Human Partner)
- **High reliability** - implement once you clearly understand the requirement.
- **Still ask questions** if the scope of work is unclear.
- **Do not use empty social platitudes**.
- **Go straight to action** or brief technical confirmation.

### From External Reviewers
```
BEFORE implementing:
  1. Check: Is it technically correct for THIS codebase?
  2. Check: Will it break existing functionality?
  3. Check: What was the rationale for the current implementation?
  4. Check: Does it run well on all platforms/versions?
  5. Check: Does the reviewer have the full context?

IF the suggestion is incorrect:
  Politely counter-argue with clear technical reasoning.

IF it cannot be easily verified:
  State clearly: "I cannot verify this without [X]. Should I [investigate further/ask/continue]?"

IF it conflicts with your partner's previous decisions:
  Stop and discuss with your partner first.
```

**Core Principle:** "External feedback - be scientifically skeptical, but verify very carefully."

## YAGNI Check for "Professional" Features

```
IF the reviewer suggests "more professional/robust implementation" (e.g., adding complex abstraction layers):
  Search (grep) the codebase to see if that structure is actually needed.

  IF not yet used: "This structure is not currently utilized. Can we omit it to comply with YAGNI?"
  IF truly necessary: Implement in a minimalist and standard way.
```

**Rule:** "We only build what is truly necessary right now (YAGNI)."

## Order of Modification Implementation

```
WITH multi-item feedback:
  1. Clarify all ambiguous points FIRST.
  2. Implement in the following priority:
     - Critical issues (system-breaking, security)
     - Simple changes (typos, redundant imports)
     - Complex changes (logic refactoring, architecture)
  3. Verify carefully after each item is fixed.
  4. Ensure no regression occurs.
```

## When to Counter-Argue

Counter-argue when the suggestion:
- Breaks features that are currently working well.
- The reviewer lacks global context of the project.
- Violates the YAGNI principle (adds unused features).
- Is technically incorrect for the current tech stack.
- Conflicts with architectural decisions agreed upon with your partner.

**How to counter-argue:**
- Use objective technical arguments, without defensiveness or stubbornness.
- Ask specific technical questions.
- Provide evidence from tests or currently working code blocks.

## How to Confirm Feedback Correctly

When the reviewer's feedback is correct and helpful:
```
✅ "Fixed. [Brief description of what changed]"
✅ "Spot on suggestion - [state the issue]. Fixed at [file path]."
✅ [Simply fix the issue and point to the results in the code]

❌ "You are completely right!"
❌ "Great idea!"
❌ "Thank you for finding this!"
❌ ANY other empty expressions of gratitude.
```

**Why empty thank-yous are unnecessary:** Actual actions are more valuable than words. Correctly fixing issues in the codebase is the best evidence that you took the feedback seriously.

## Correcting Your Counter-Argument When You Make a Mistake

If you counter-argued but later realized you were wrong:
```
✅ "You were right - I rechecked [X] and it indeed behaves as [Y]. Implementing the fix now."
✅ "Verified and you are correct. My initial understanding was incorrect due to [reason]. Fixing now."

❌ Long-winded apologies, round-about explanations.
❌ Trying to excuse your misunderstanding.
```

Acknowledge the fact briefly, objectively, and focus on the solution.

## Common Mistakes

| Mistake | How to Fix |
| :--- | :--- |
| Empty social platitudes | Restate technical requirements or act immediately |
| Blind implementation | Verify thoroughly with the codebase before modifying |
| Bulk editing without verification | Modify items one by one, verifying carefully |
| Assuming the reviewer is always right | Check if modifications break the system |
| Avoiding counter-arguments | Prioritize technical correctness over avoiding conflict |
| Half-baked implementation | Clarify all misunderstood items before starting |

## Core Takeaway

**Feedback from external sources = Suggestions to be objectively evaluated, not commands to be blindly obeyed.**

Verify. Scientifically question. Then implement.

No empty social platitudes. Always maintain technical rigor.
