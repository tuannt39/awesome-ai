---
name: using-git-worktrees
description: Use when starting feature work that needs isolation from the current workspace or before executing an implementation plan - ensures isolated workspace exists via native tool or fallback git worktree
---

<ACTIVATION-GATE>
According to GEMINI.md rules: "ABSOLUTELY FORBIDDEN to use git worktree directly unless explicitly requested by the user."
Only use this skill when the user explicitly requests workspace isolation using git worktree.
The standard method on Antigravity CLI is using `Workspace: "branch"` when calling `invoke_subagent` instead of manually creating a git worktree.
</ACTIVATION-GATE>

# Using Git Worktrees

## Overview

Ensure work is conducted in an isolated workspace. Prioritize the native workspace management tools of the platform you are using. Only use manual git worktree as a last resort fallback when no native tools are supported.

**Core Principle:** Check if isolation already exists first. Then use platform native tools. Finally, use manual git worktree as a fallback. Absolutely do not interfere unauthorizedly with existing management systems.

**Announcement at start:** "I am using the using-git-worktrees skill to establish an isolated workspace."

## Step 0: Detect Current Isolated State

**Before creating anything, check if you are already in an isolated workspace.**

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

**Submodule Warning:** The condition `GIT_DIR != GIT_COMMON` is also true when you are inside git submodules. Before concluding that you are "in a worktree," verify that you are not inside a submodule:

```bash
# If this command returns a path, you are in a submodule, not a worktree — treat it as a normal repository
git rev-parse --show-superproject-working-tree 2>/dev/null
```

**If `GIT_DIR != GIT_COMMON` (and not a submodule):** You are already in a linked worktree. Skip creation steps and proceed directly to Step 3 (Project Setup). ABSOLUTELY DO NOT create another worktree.

Report current branch status:
- On a branch: "Already in an isolated workspace at `<path>` on branch `<name>`."
- Detached HEAD: "Already in an isolated workspace at `<path>` (detached HEAD, externally managed). Will need to create branch upon completion."

**If `GIT_DIR == GIT_COMMON` (or inside a submodule):** You are in a standard checked-out git directory.

Check if the user has specified a worktree option in the instructions. If not, ask for permission before creating:

> "Would you like me to set up an isolated git worktree? This helps protect your current branch from unexpected changes."

Always respect user preferences. If they decline, work in place and skip to Step 3.

### Antigravity CLI: Native Workspace Isolation (Recommended)

On Antigravity CLI, the standard and safest method is using the `Workspace` property when calling `invoke_subagent` instead of manually creating a git worktree:

| Workspace Mode | Equivalent | When to Use |
|----------------|------------|-------------|
| `"inherit"` | Same current directory | Tasks that need to share state |
| `"branch"` | `git worktree add` | Full isolation for new feature work |
| `"share"` | Shared storage | Independent branch but do not want to duplicate resources |

If this native isolation mechanism is supported, skip Steps 1-2 entirely and proceed directly to Step 3 (Project Setup).

## Step 1: Create Isolated Workspace (Fallback)

**You have two mechanisms. Try them in order of priority below.**

### 1a. Use Platform Native Tools (Preferred)

If the user agreed to set up an isolated workspace (Step 0). Do you have native tools to create a worktree? It could be a tool named `EnterWorktree`, `WorktreeCreate`, or a `/worktree` command, `--worktree` flag. If available, use it and skip to Step 3.

Native tools automatically handle directory locations, branch creation, and safe cleanups. Running `git worktree add` manually when native tools are present creates hidden states that the management system cannot track.

Only proceed to Step 1b if you absolutely do not have any native tools supported.

### 1b. Use Manual Git Worktree (Fallback Only)

**Only use this method if Step 1a is unavailable** — you have no native tools. Create a worktree manually using git commands.

#### Choose Directory

Follow the order of priority below. Explicit user preferences are always number one.

1. **Check instructions for a specified worktree directory.** If provided, use it immediately.
2. **Check if project local worktree directories already exist:**
   ```bash
   ls -d .worktrees 2>/dev/null     # Preferred (hidden)
   ls -d worktrees 2>/dev/null      # Alternative
   ```
   If found, use it. If both exist, prioritize `.worktrees`.
3. **Check global directory:**
   ```bash
   project=$(basename "$(git rev-parse --show-toplevel)")
   ls -d ~/.config/superpowers/worktrees/$project 2>/dev/null
   ```
   If found, use it to ensure backward compatibility.
4. **If there are no other instructions**, default to creating a `.worktrees/` directory at the project root.

#### Safety Verification (Project Local Directories Only)

**MANDATORY to verify that this directory is added to `.gitignore` before creating the worktree:**

```bash
git check-ignore -q .worktrees 2>/dev/null || git check-ignore -q worktrees 2>/dev/null
```

**If NOT ignored:** Add the directory to `.gitignore`, commit this change, and then proceed.

**Why this is extremely important:** Avoids accidentally committing the entire contents of the worktree into the main repository.

Global directories (`~/.config/superpowers/worktrees/`) do not need this check.

#### Create Worktree

```bash
project=$(basename "$(git rev-parse --show-toplevel)")

# Determine path based on chosen location
# Local: path="$LOCATION/$BRANCH_NAME"
# Global: path="~/.config/superpowers/worktrees/$project/$BRANCH_NAME"

git worktree add "$path" -b "$BRANCH_NAME"
cd "$path"
```

**Sandbox Fallback:** If the `git worktree add` command fails due to permission errors (blocked by sandbox), report to the user and switch to working directly in the current directory. Then proceed with setup and dry-run tests in place.

## Step 3: Project Setup

Automatically detect and run the appropriate setup commands for the project:

```bash
# Node.js
if [ -f package.json ]; then npm install; fi

# Rust
if [ -f Cargo.toml ]; then cargo build; fi

# Python
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
if [ -f pyproject.toml ]; then poetry install; fi

# Go
if [ -f go.mod ]; then go mod download; fi
```

## Step 4: Verify Clean Baseline

Run the test suite to ensure the initial workspace is completely clean with no pre-existing failures:

```bash
# Use commands appropriate for the project
npm test / cargo test / pytest / go test ./...
```

*Note:* ABSOLUTELY DO NOT write any new tests during this step.

**If tests are pre-failing:** Report immediately to the user and ask whether to proceed or stop to fix the pre-existing failures first.

**If tests pass:** Report ready status.

### Sample Report

```
Worktree is ready at <absolute-path>
Initial test suite is clean (<N> tests passed, 0 failures)
Ready to implement feature <feature-name>
```

## Quick Reference Table

| Situation | Action |
|-----------|--------|
| Already in linked worktree | Skip creation, proceed to setup (Step 0) |
| Inside a submodule | Treat as normal repository (guarded in Step 0) |
| Native worktree tool exists | Must use native tool (Step 1a) |
| No native tools | Use manual git worktree as fallback (Step 1b) |
| Local `.worktrees/` exists | Use it (ensure ignored) |
| Local `worktrees/` exists | Use it (ensure ignored) |
| Both exist | Use `.worktrees/` |
| Neither exists | Default to `.worktrees/` and add to `.gitignore` |
| Directory not ignored | Must add to `.gitignore` and commit before creating |
| Permission error during creation | Sandbox fallback, work in place |
| Pre-existing test failures | Report failures and ask for input before coding |

## Common Mistakes and How to Fix

### Overusing manual git worktree
- **Issue:** Arbitrarily running `git worktree add` when the platform (Antigravity CLI) already supports native workspace isolation.
- **Fix:** Always prioritize using `Workspace: "branch"` when calling `invoke_subagent`.

### Skipping status check
- **Issue:** Creating a nested worktree inside an already isolated worktree.
- **Fix:** Always run Step 0 checks on `GIT_DIR` and `GIT_COMMON` before doing anything.

### Skipping directory ignore
- **Issue:** Worktree contents get tracked by git, polluting git status.
- **Fix:** Always use `git check-ignore` and add to `.gitignore` before creating.

## Red Flags (Do Not)

- **Never** create a worktree when Step 0 detects you are already in an isolated workspace.
- **Never** run `git worktree add` manually if the platform supports native isolation or native tools. This is the most common mistake.
- **Never** skip `.gitignore` verification for local project directories.
- **Never** skip the initial test suite run to verify a clean baseline.
- **Never** write new tests during setup and baseline steps.
