---
name: writing-skills
description: Use when creating new skills, editing existing skills, or verifying skill functionality before deployment.
---

# Writing Skills

## Overview

**Writing skills is applying Test-Driven Development (TDD) to standard operating procedure documentation.**

**Antigravity CLI personal skills directory:** `~/.gemini/antigravity-cli/skills/`

You write test cases (stress-inducing hypothetical scenarios for subagents), watch them fail (baseline behavior without the skill), write the skill documentation, watch them pass (subagents adhering to the skill), and then refactor (closing argument loopholes).

**Core Principle:** If you have never seen a subagent fail without the skill, you cannot know whether that skill correctly guides the necessary behavior.

**MANDATORY REQUIREMENT:** You MUST master the `test-driven-development` skill before using this skill. That skill defines the fundamental RED-GREEN-REFACTOR cycle. This skill applies TDD to writing documentation.

## What is a Skill?

A **skill** is a reference guide for techniques, design patterns, or tools that have proven effective. Skills make it easy for future AI subagent instances to search and apply optimal solutions.

**A skill is:** Reusable techniques, design patterns, tool guides, reference documentation.

**A skill is NOT:** A narrative log of how you solved a specific problem.

## TDD Mapping Applied to Skills

| TDD Concept | Application to Skill Writing |
| :--- | :--- |
| **Test case** | Stressful hypothetical scenario for subagents |
| **Production code** | The skill document file (SKILL.md) |
| **Failing test (RED)** | Subagent violating rules when lacking the skill |
| **Passing test (GREEN)** | Subagent strictly adhering when the skill is present |
| **Refactoring (Refactor)** | Closing argument loopholes while maintaining compliance |
| **Write test first** | Try the hypothetical scenario BEFORE writing the skill |
| **Observe failure** | Record the exact rationalizations the subagent uses to evade rules |
| **Minimalist production code** | Write skill content focused exactly on those violations |
| **Observe pass** | Verify the subagent is now fully compliant |
| **Refactoring cycle** | Detect new rationalizations -> close loopholes -> re-verify |

The entire process of creating a skill strictly adheres to the RED-GREEN-REFACTOR cycle.

## When to Create a Skill

**Create when:**
- The technique is not obvious or easily confused.
- You will need to refer to this technique across multiple different projects.
- The design pattern is broadly applicable (not specific to a single project).
- It provides significant value to other team members.

**Do not create when:**
- The solution is a one-off for an isolated issue.
- Standard conventions are already fully documented elsewhere.
- Conventions are highly specific to a project (record these in the project's CLAUDE.md or GEMINI.md instead).

## Skill Directory Structure

```
skills/
  skill-name/
    SKILL.md              # Main reference document (mandatory)
    supporting-files.*    # Only when absolutely necessary (sample code, helpers)
```

**Do not nest directories** - all skills reside in a single flat space for easy searchability.

**Separate files for:**
1. **Large reference documents** (over 100 lines) - detailed API docs, full specs.
2. **Reusable tools** - test run scripts, config template files.

**Write directly in SKILL.md for:**
- Core principles and concepts.
- Minimalist code samples (under 50 lines).

## Standard Structure of a SKILL.md File

**Configuration Metadata (YAML Frontmatter):**
- Contains two mandatory fields: `name` (skill name) and `description` (description).
- Total YAML frontmatter size maximum of 1024 characters.
- `name`: Use only lowercase letters, numbers, and hyphens (no special characters or brackets).
- `description`: Written in the third person, **only describes WHEN to use the skill** (DO NOT summarize the skill's workflow or solution).
  - Starts with the phrase "Use when..." to focus on the trigger condition.
  - Clearly describes specific symptoms, situations, and contexts to apply.
  - Keep length under 500 characters.

```markdown
---
name: lowercase-skill-name
description: Use when [detailed description of trigger conditions and symptoms]
---

# Skill Name

## Overview
What is this concept? The core principle in 1-2 concise sentences.

## When to Use
- List of specific symptoms indicating this skill is needed.
- When NOT to use.

## Core Design Pattern
Visual comparison between Bad code and Good code.

## Quick Reference
Tables or bullet points summarizing for easy lookup.

## Practical Implementation
Visual, clear code snippets.

## Common Mistakes
Common pitfalls during application and how to fix them.
```

## Search Optimization (Claude/Gemini Search Optimization - CSO)

### 1. High-Quality Description Field
**Goal:** AI reads this description to decide whether to load this skill into the current working context.
**Critical Rule:** The description only states TRIGGER CONDITIONS, not a summary of the solution or workflow. If the description summarizes the workflow, the AI will tend to follow that summary and skip reading the detailed skill file below.

```yaml
# ❌ BAD: Summarizes the workflow (AI will skip the detailed file)
description: Use when executing plans - trigger subagents for each task and run code reviews between tasks.

# ✅ GOOD: States only trigger conditions and application context
description: Use when executing large implementation plans containing multiple independent tasks in the current session.
```

### 2. Use Realistic Search Keywords
Include keywords that AI frequently searches for when encountering errors:
- Classic error messages: "Hook timed out", "ENOTEMPTY", "race condition".
- Error symptoms: "flaky", "hangs", "memory leak", "conflict".
- Actual system tools and commands.

## Iron Law (Similar to TDD)

```
DO NOT CREATE A NEW SKILL IF THERE IS NO PRE-EXISTING FAILING TEST CASE
```

This rule applies to both creating NEW skills and EDITING existing skills.

## Testing Skill Types with Subagents

### Discipline Enforcement Skills (TDD, Verification)
- **Test by:** Putting subagents in high-pressure situations (out of time, high sunk cost, fatigue).
- **Success criteria:** Subagent strictly adheres to the disciplined process despite the pressure.

### Technical Skills (Step-by-step guidance)
- **Test by:** Requesting the subagent to apply the technique to a new complex problem.
- **Success criteria:** Subagent successfully applies it and handles edge cases well.

## Closing AI Rationalization Loopholes

AI is extremely smart and will find ways to bypass rules under progress pressure. Your skills must be designed to block all rationalizations.

### Explicitly Block Exception Arguments:
Do not just state general rules, explicitly forbid specific evasive behaviors:

<Bad>
```markdown
If you write code before writing tests? Delete it.
```
</Bad>

<Good>
```markdown
If you write code before writing tests? Delete it and start over.

**Absolutely no exceptions:**
- Do not keep it as a "reference."
- Do not "adapt" it while writing tests.
- Do not look back at it for ideas.
- Deleting means completely wiping it from memory.
```
</Good>

### Provide an AI Rationalization Confrontation Table:
Collect all rationalizations the AI used during the RED phase and place them in a direct confrontation table in the skill document.

## Skill Creation Process Checklist

**RED Phase - Write Failing Test:**
- [ ] Set up stress-inducing hypothetical scenarios with the subagent.
- [ ] Run the subagent WITHOUT the skill and record all its rationalizations/violations.

**GREEN Phase - Write Minimalist Skill:**
- [ ] Define standardized skill name (lowercase, numbers, hyphens).
- [ ] Declare precise YAML frontmatter (name, description starting with "Use when...").
- [ ] Write skill content focused on thoroughly resolving the recorded violations.
- [ ] Run the subagent WITH the skill and verify compliance.

**REFACTOR Phase - Close Loopholes:**
- [ ] Detect new rationalizations emerging from the run with the skill.
- [ ] Update the rationalization confrontation table and red flags list in the document.
- [ ] Re-run until the subagent is 100% compliant.

## Core Takeaway

**Writing skill documents is doing TDD for standard operating procedures.**

Same Iron Law: No skill without a pre-existing failing test first.
Same cycle: RED -> GREEN -> REFACTOR.
Brings same value: Outstanding quality, absolute confidence, sustainable results.
