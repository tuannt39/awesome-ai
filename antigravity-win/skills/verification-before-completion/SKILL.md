---
name: verification-before-completion
description: Use when preparing to declare work completed, a bug fixed, or tests passed, before committing or creating a Pull Request.
---

# Verification Before Completion

## Overview

Declaring work completed without verifying it is dishonest, not efficient.

**Core Principle:** Always provide evidence before asserting results.

**Violating the letter of this rule is violating the spirit of this rule.**

## The Iron Rule

```
DO NOT DECLARE COMPLETION WITHOUT THE LATEST VERIFICATION EVIDENCE
```

If you have not run verification commands during the current work session, you are not allowed to claim the system works correctly.

## The Gate Function

```
BEFORE declaring any status or expressing satisfaction:

1. IDENTIFY: What command or action proves this claim?
2. RUN: Execute the command COMPLETELY (fresh, independent run)
3. READ: Carefully read all output, check exit codes, count errors/warnings
4. VERIFY: Does the output confirm your claim?
   - If NO: Report actual status with specific evidence
   - If YES: Assert the claim ACCOMPANIED BY clear evidence
5. ONLY THEN: Declare completion

Skipping any step = guessing, not verifying
```

## Common Failures

| Claim | Mandatory Requirement | What does NOT qualify |
| :--- | :--- | :--- |
| Tests passed | Test command output: 0 failures | Previous run, confident "it should run" |
| Linter clean | Linter output: 0 errors | Checking part of the file, speculation |
| Build succeeded | Build command: exit code 0 | Linter runs fine, log looks okay |
| Bug fixed | Retried original bug symptoms: gone | Modified code and think it is fixed |
| Regression test works | Verified Red-Green cycle | Test succeeded only once |
| Subagent completed | Compared actual Git diff | Subagent reports "success" |
| Project requirements met | Line-by-line checklist comparison | Technical tests pass |

## Red Flags - STOP

- Using vague words like: "should", "probably", "looks like", "maybe".
- Expressing satisfaction before verification ("Great!", "Perfect!", "Done!").
- Preparing to commit/push/create a PR without re-verifying from scratch.
- Trusting subagent success reports blindly.
- Verifying only a small part and speculating for the whole.
- Giving in to "just this once".
- Tired and wanting to finish work quickly.
- **ANY phrasing implying success without actually running verification commands.**

## Common Excuses

| Excuse | Reality |
| :--- | :--- |
| "The code definitely runs fine now" | RUN the actual verification command. |
| "I am completely confident" | Confidence is not equivalent to actual evidence. |
| "Just this once" | Absolutely no exceptions. |
| "The linter passed" | Linter passing does not mean the code compiles successfully. |
| "Subagent reported success" | You must check independently and cross-reference the Git diff. |
| "I am tired" | Fatigue is not an excuse to lower the quality of work. |

## Practical Verification Commands on Windows (PowerShell)

### Verification quality WITHOUT writing new tests:
> [!IMPORTANT]
> According to the strict policy of `GEMINI.md`, if the user does not request writing new tests, the AI absolutely must not write extra tests for verification. Instead, prioritize the following safe static and dynamic verification methods:

**1. Compilation and System Build:**
```powershell
# Run project build
npm run build
# Or for .NET / Go / Rust projects
dotnet build
go build ./...
cargo build
```
*Ensure compilation finishes with exit code 0 and no critical errors.*

**2. Type Checking:**
```powershell
# Check TypeScript
npx tsc --noEmit
```
*Ensure no type errors are introduced.*

**3. Syntax and Code Quality Linting:**
```powershell
# Run linter
npm run lint
# Or run specific linter
npx eslint . --max-warnings 0
```
*Require 0 errors and 0 warnings.*

**4. Manual Execution and Logs Checking:**
```powershell
# Run the application or CLI command to check actual results
node dist/index.js --help
# Check the latest log file generated on Windows
Get-Content -Path .\logs\app.log -Tail 50
```
*Observe real logs to ensure no hidden warnings or exceptions.*

**5. Check Actual Git Changes (Git Diff):**
```powershell
# Compare actual changes
git diff
# Check list of changed files
git status
```
*Ensure no redundant files or out-of-scope modifications are committed.*

## Why This Matters

From real-world lessons:
- Partner says "I do not trust you" -> trust is severely broken.
- Undefined functions pushed -> crash system in production.
- Key requirements missing but still delivered -> incomplete feature.
- Time wasted fixing silly errors due to lack of verification.

## The Core Point

**There are no shortcuts for verification.**

Run the actual command. Read the output. ONLY THEN declare the result.

This is a non-negotiable principle.
