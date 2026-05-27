---
name: executing-plans
description: Use when you already have a detailed written implementation plan to execute in an isolated session with evaluation checkpoints
---

# Executing Plans

## Overview

Load the plan, critically evaluate it, execute all tasks, and report on completion.

**Announcement at start:** "I am using the executing-plans skill to implement this plan."

**Note:** Inform the user that the system works significantly better when it has access to subagents. The quality of work will be considerably higher if run on a platform that supports subagents (such as Claude Code or Antigravity CLI). If the platform supports subagents, prioritize using the `subagent-driven-development` skill instead of this one.

**Platform Note (Antigravity CLI):** Use the `task.md` artifact instead of `TodoWrite`. Use `invoke_subagent` instead of the `Task` tool. See the `using-superpowers/references/antigravity-tools.md` documentation for details.

## Execution Workflow

### Step 1: Load and Evaluate the Plan
1. Read the plan file carefully.
2. Critically evaluate it - identify any questions or concerns about the feasibility of the plan.
3. If there are concerns: Communicate and clarify them with the user before starting.
4. If there are no concerns: Create the `task.md` artifact file to track progress and proceed.

### Step 2: Execute Tasks

For each task in `task.md`:
1. Mark the task status as `in_progress`.
2. Follow each outlined step precisely (plans usually contain extremely detailed steps).
3. **TESTING POLICY:** Absolutely do not write new tests unless explicitly requested. Verify the product by running build, typecheck, lint, or existing tests.
4. Run the verification steps as specified by the requirements.
5. Mark the task status as `completed`.

*Note:* All communication and feedback during execution must be in Vietnamese (as required by local instructions).

### Step 3: Finish Development

Once all tasks in `task.md` are completed and successfully verified:
- Announce: "I am using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SKILL:** Use the `superpowers:finishing-a-development-branch` skill to check the entire system and merge the branch.

## When to Stop and Ask for Help

**STOP execution immediately when:**
- You encounter a blocker (missing dependencies, critical test failures, unclear instructions).
- The plan has severe gaps that prevent you from starting or continuing.
- You do not understand a specific instruction in the plan.
- Verification steps fail repeatedly despite attempts to fix them.

**Stop to clarify instead of guessing.**

## When to Revisit Previous Steps

**Go back to Step 1 (Evaluate Plan) when:**
- The user updates the plan based on your feedback.
- The core approach needs to be completely rethought due to discovering a major issue during execution.

**Absolutely do not try to push through blockers blindly** - stop and discuss.

## Rules to Remember
- Always critically evaluate the plan first.
- Follow the steps in the plan precisely.
- Never skip verification steps.
- Stop when blocked, do not guess.
- Never start implementing directly on the main branch (main/master) without explicit user consent.
- Do not write new tests unless there is an explicit request.
