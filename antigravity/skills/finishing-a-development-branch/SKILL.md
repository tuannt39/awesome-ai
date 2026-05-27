---
name: finishing-a-development-branch
description: Use when implementation is complete, all tests pass, and you need to decide how to integrate the code — guides completion of work by presenting clear options for merge, PR, or cleanup.
---

<PLATFORM-NOTE>
On platforms with strict Git policies (e.g., GEMINI.md specifies "no arbitrary Git commits/merges unless specifically requested"), ALL Git operations in this skill (merge, push, branch deletion, worktree removal) require explicit user consent before execution. Present the options but DO NOT automatically execute.
</PLATFORM-NOTE>

# Finishing a Development Branch

## Overview

Guides the completion of development work by presenting clear options and handling the chosen workflow.

**Core Principle:** Verify tests -> Detect environment -> Present options -> Execute choice -> Clean up.

**Announcement at start:** "I am using the finishing-a-development-branch skill to complete this work."

## Workflow Steps

### Step 1: Verify Tests

**Before presenting options, ensure that all tests pass successfully:**

```powershell
# Run the project's test suite on Windows PowerShell
npm test
# Or the corresponding commands depending on the project's programming language
cargo test
pytest
go test ./...
```

**If tests fail:**
```
Detected test failures (<N> errors). Must be fixed before finishing:

[Display list of errors]

Cannot proceed with merge/PR until all tests pass.
```

Stop. Absolutely do not proceed to Step 2.

**If all tests pass:** Proceed to Step 2.

### Step 2: Detect Environment

**Determine the state of the workspace before presenting options:**

```powershell
# On Windows PowerShell, identify the actual git directory
$GIT_DIR = git rev-parse --git-dir
$GIT_COMMON = git rev-parse --git-common-dir
```

This helps determine which options menu to display and how cleanup will be handled later:

| State | Menu Displayed | Cleanup Method |
| :--- | :--- | :--- |
| Normal Repo (`$GIT_DIR` matches `$GIT_COMMON`) | Full 4 standard options | No worktree to clean up |
| Worktree with a clear branch name | Full 4 standard options | Clean up based on origin (see Step 6) |
| Detached HEAD | Reduced to 3 options (no merge) | No cleanup (managed by external tool) |

### Step 3: Determine the Base Branch

```powershell
# Search for common base branches (main or master)
git merge-base HEAD main
# Or ask directly: "This development branch was split from the main branch - is this correct?"
```

### Step 4: Present Options

**For standard Repos or Worktrees with a clear branch name — display exactly these 4 options:**

```
Implementation is complete. How would you like to proceed?

1. Perform local merge into <base-branch>
2. Push to remote and create a Pull Request
3. Keep the branch state as is (I will handle it myself later)
4. Discard this work entirely

Please select the corresponding number?
```

**For Detached HEAD state — display exactly these 3 options:**

```
Implementation is complete. You are in a Detached HEAD state (workspace managed by an external tool).

1. Push to remote as a new branch and create a Pull Request
2. Keep the current state (I will handle it myself later)
3. Discard this work entirely

Please select the corresponding number?
```

**Do not add long-winded explanations** - keep the options short and visual.

### Step 5: Execute Choice

#### Option 1: Local Merge

```powershell
# Return to the root directory of the main repo to ensure path safety
$MAIN_ROOT = git rev-parse --show-toplevel
cd $MAIN_ROOT

# Perform merge — ensure success before cleaning up anything
git checkout <base-branch>
git pull
git merge <feature-branch>

# Re-run tests on the merged result
npm test

# Only after a successful merge: proceed with worktree cleanup (Step 6), then delete the branch
git branch -d <feature-branch>
```

#### Option 2: Push and Create PR

```powershell
# Push the branch to remote
git push -u origin <feature-branch>

# Create PR using the official CLI (if gh tool is available)
gh pr create --title "<summary-title>" --body "## Summary of Changes`n- <list 2-3 key points>`n`n## Verification Plan`n- [ ] <verification steps>"
```

**ABSOLUTELY DO NOT clean up the worktree** — you or your partner may need to keep this environment to make further edits based on code review feedback.

#### Option 3: Keep Current State

Report clearly: "Keeping the branch <branch-name> as is. Worktree is preserved at <actual-path>."

**Do not clean up the worktree.**

#### Option 4: Discard

**Confirmation is mandatory before executing:**
```
This action will permanently delete:
- Branch <branch-name>
- All unpushed commits: <commit-list>
- Worktree at <path>

Please type exactly 'discard' to confirm deletion.
```

Wait for the partner to type exactly 'discard'.

If confirmed:
```powershell
$MAIN_ROOT = git rev-parse --show-toplevel
cd $MAIN_ROOT
# Perform worktree cleanup (Step 6), then force delete the branch:
git branch -D <feature-branch>
```

### Step 6: Clean Up Workspace

**Only perform this step for Option 1 and Option 4.** Option 2 and Option 3 always preserve the worktree.

```powershell
$WORKTREE_PATH = git rev-parse --show-toplevel
```

**If it is a standard Repo (`$GIT_DIR` matches `$GIT_COMMON`):** No extra worktree to clean up. Complete.

**If the worktree path is within temporary management directories (`.worktrees/`, `worktrees/`):** We have ownership and proceed to clean up:

```powershell
$MAIN_ROOT = git rev-parse --show-toplevel
cd $MAIN_ROOT
git worktree remove "$WORKTREE_PATH"
git worktree prune  # Thoroughly clean up orphaned registrations
```

**Otherwise:** If the workspace is managed by an external system, absolutely do not delete it arbitrarily. Leave the state as is and report.

## Common Mistakes

- **Skipping test execution:** Merging broken code into the main branch -> Always run tests before proposing options.
- **Asking vague open-ended questions:** Confuses the partner -> Present precisely the designed options menu.
- **Deleting worktree/branch upon creating a PR:** Destroys the environment needed to fix review comments -> Only clean up when merged successfully or discarded entirely.
- **Running worktree remove while CWD is inside that worktree:** The command will fail -> Change directory (CWD) back to the main repo before running the delete command.

## Red Flags

- Merging when tests are failing.
- Discarding development work without explicit confirmation of the word "discard" from the partner.
- Arbitrarily running destructive Git commands without explicit partner consent (a severe violation of GEMINI.md policy).
