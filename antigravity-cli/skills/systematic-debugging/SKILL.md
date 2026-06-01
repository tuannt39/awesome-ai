---
name: systematic-debugging
description: Use when encountering any bug, failing test, or unexpected behavior, before proposing a fix.
---

# Systematic Debugging

## Overview

Fixing bugs randomly wastes time and creates new bugs. Quick patches only hide the underlying core issues.

**Core Principle:** ALWAYS find the root cause before attempting to fix the bug. Fixing symptoms is a failure.

**Violating the letter of this process is violating the spirit of systematic debugging.**

## The Iron Rule

```
DO NOT FIX A BUG WITHOUT INVESTIGATING THE ROOT CAUSE FIRST
```

If you have not completed Phase 1, you are not allowed to propose fix solutions.

## When to Use

Use for ANY technical issue:
- Failing tests
- Production errors
- Unexpected behavior
- Performance issues
- System build failures
- Integration issues

**Especially use when:**
- Under time pressure (urgency makes you prone to guessing)
- A quick fix seems obvious
- You tried multiple fixes before but they didn't work
- The previous solution did not work
- You do not fully understand the issue

**Do not skip when:**
- The issue seems simple (simple bugs have root causes too)
- You are in a hurry (hurrying ensures you'll have to start over)
- Management wants it fixed IMMEDIATELY (systematic debugging is always faster than trial-and-error guessing)

## The Four Phases of Debugging

You MUST complete each phase before moving to the next.

### Phase 1: Root Cause Investigation

**BEFORE attempting to fix any bug:**

1. **Read Error Messages Thoroughly**
   - Do not ignore errors or warnings.
   - They often contain the exact solution.
   - Read the entire call stack trace. Use the `view_file` tool to open log or trace files instead of generic read commands.
   - Note line numbers, file paths, and error codes.

2. **Establish a Reliable Reproduction (Integrating strict GEMINI.md test policy)**
   - Can you reliably trigger the error?
   - What are the exact steps?
   - Does the error happen every time?
   - If not reproducible -> gather more data via existing logs/traces instead of guessing.
   - **STRICT TEST POLICY:** Absolutely do not write new test files, fixtures, or mocks to reproduce the error unless the user explicitly requests it (e.g., "write tests", "TDD"). Reproduce by running commands manually via `run_command`, observing logs, or using existing test suites.

3. **Check Recent Changes**
   - What changed recently that could have caused this?
   - Git diff, recent commits.
   - New dependencies, configuration changes.
   - Environment differences.

4. **Gather Evidence in Multi-Component Systems**

   **WHEN the system has multiple components (CI -> build -> code sign, API -> service -> database):**

   **BEFORE proposing solutions, add diagnostic logging:**
   ```
   For EACH component boundary:
     - Log data entering the component
     - Log data exiting the component
     - Verify configuration/environment variable propagation
     - Check status at each layer
   
   Run once to gather evidence of WHERE the error occurs
   THEN analyze evidence to identify the failing component
   THEN investigate that specific component in detail
   ```

   **Example (Multi-layered System):**
   ```bash
   # Layer 1: Check environment variables in current session
   echo "=== Secrets available in workflow ==="
   if [ -n "$IDENTITY" ]; then echo "IDENTITY: SET"; else echo "IDENTITY: NOT SET"; fi

   # Layer 2: Check inside build script
   echo "=== Environment variables in build script ==="
   env | grep IDENTITY

   # Layer 3: Check certificate/key files
   echo "=== Certificate/key files ==="
   ls -la ~/.ssh/ 2>/dev/null; ls -la /etc/ssl/certs/ 2>/dev/null | head -5
   ```

   **This reveals:** Which layer is failing (secret -> workflow ✓, workflow -> build ✗).

5. **Trace Data Flow**

   **WHEN the bug is deep in the call stack:**
   - Where did the incorrect value originate?
   - Which function called this function with that incorrect value?
   - Keep tracing backward until you find the true source.
   - Fix at the source, not the symptom.

### Phase 2: Pattern Analysis

**Find the correct design/behavior pattern before fixing:**

1. **Find Working Examples**
   - Identify similar code running successfully in the same codebase.
   - What is working that is similar to the failing part?

2. **Compare with Reference Documentation**
   - If implementing a design pattern, read the documentation and reference code THOROUGHLY.
   - Do not skim - read line-by-line to understand fully before applying.

3. **Identify Differences**
   - What is different between the working part and the failing part?
   - List every difference, no matter how small.
   - Do not assume "that doesn't matter".

4. **Understand Dependencies**
   - What else does this component need to run (config, environment, resources)?
   - What assumptions are made in the code?

### Phase 3: Hypothesis and Verification

**Apply the scientific method:**

1. **Formulate a Single Hypothesis**
   - State clearly: "I think X is the root cause because Y".
   - Write down this hypothesis.
   - Be specific, not vague.

2. **Minimal Verification**
   - Make the SMALLEST change possible to test the hypothesis.
   - Change only one variable at a time.
   - Do not fix multiple things at once.

3. **Verify Before Proceeding**
   - Did the change work? Yes -> Move to Phase 4.
   - No? Formulate a NEW hypothesis.
   - ABSOLUTELY DO NOT stack other quick fixes on top.

4. **When You Do Not Know**
   - Say frankly: "I do not understand X fully".
   - Do not pretend to know.
   - Research further or discuss with your partner.

### Phase 4: Implementation of the Fix

**Fix the root cause, not the symptom:**

1. **Create a Failing Test Case (Only when explicitly requested by the user)**
   - Write the simplest possible reproduction test case.
   - MUST be done before fixing (if allowed to write tests; see `test-driven-development`).
   - **Note:** If project policy forbids writing new tests (like strict `GEMINI.md` mode without explicit request), switch to alternative verification: reproduce via manual run, log checks, or running existing test suites via the `run_command` tool.

2. **Implement a Single Fix**
   - Address the identified root cause directly.
   - Change ONE thing at a time.
   - Do not clean up unrelated parts along the way.
   - Do not mix refactoring into the bug fix.

3. **Verify the Fix**
   - Does the test/run pass now?
   - No regressions in other features?
   - Is the problem fully resolved at the root?

4. **If the Fix Does Not Work**
   - STOP.
   - Count how many fixes you have tried.
   - If under 3 times: Go back to Phase 1, analyze again with newly gathered info.
   - **If 3 or more times: STOP and question the architecture (step 5 below).**
   - ABSOLUTELY DO NOT try a 4th fix without re-evaluating the architecture.

5. **If Failed 3+ Times: Question the Architecture**

   **Signs that indicate an architectural issue:**
   - Every fix exposes shared state/tight coupling/new issues elsewhere.
   - The fixes require large-scale refactoring.
   - Every fix generates new symptoms in other parts of the system.

   **STOP and ask fundamental questions:**
   - Is this design pattern actually appropriate?
   - Are we pushing a round peg into a square hole out of habit?
   - Should we change the architecture instead of patching symptoms?

   Discuss thoroughly with your partner before trying any more fixes. This is not a failed hypothesis; it is incorrect architecture.

## Red Flags - STOP and Follow the Process

Stop immediately if you have any of the following thoughts:
- "Quickly fix this first, investigate later"
- "Let's try changing X to see if it runs"
- "Change multiple things at once and run tests"
- "Skip testing, I will verify manually later"
- "It is probably X, let me fix it directly"
- "I don't fully understand, but this should work"
- "The pattern says so, but I will customize it differently"
- "Here are the main issues: [lists solutions without prior investigation]"
- Proposing solutions before tracing the data flow.
- **"Let's try just one more fix" (when already tried 2+ times)**
- **Every fix exposes a new issue elsewhere**

**ALL the above mean: STOP. Go back to Phase 1.**

**If failed 3+ times:** Question the architecture (see Phase 4.5).

## Signals from Partner That You Are off Track

**Pay attention to these course-correcting feedbacks:**
- "Does that actually happen?" -> You assumed without verifying.
- "Does it show us...?" -> You should have added diagnostic tools.
- "Stop guessing" -> You are proposing fixes without understanding.
- "Think extremely deeply about this" -> Question core principles, not just symptoms.
- "Are we stuck?" (frustrated) -> Your approach is not working.

**When seeing these signals:** STOP. Go back to Phase 1.

## Common Excuses

| Excuse | Reality |
| :--- | :--- |
| "Simple bug, no need for process" | Simple bugs have root causes too. The process resolves simple bugs extremely fast. |
| "Emergency, no time" | Systematic debugging is always FASTER than trial-and-error chaos. |
| "Just try this first, then investigate" | The first fix sets the pattern. Do it right from the start. |
| "I will write tests after verifying the fix" | Untested fixes are fragile. Testing first proves the bug is actually resolved. |
| "Fixing multiple things saves time" | You won't be able to isolate what worked and you easily introduce new bugs. |
| "Reference docs are too long, I will customize" | Half-understanding guarantees bugs. Read the full documentation. |
| "I see the issue, let me fix it immediately" | Seeing the symptom does not mean you understand the root cause. |
| "Try one more fix" (after 2+ failures) | 3+ failures point to architectural issues. Evaluate the architecture rather than patching. |

## Quick Reference Table

| Phase | Main Activity | Success Criteria |
| :--- | :--- | :--- |
| **1. Root Cause** | Read error, reproduce, check changes, gather evidence | Understand WHAT and WHY it failed |
| **2. Pattern Analysis** | Find working examples, compare details | Identify clear differences |
| **3. Hypothesis** | Formulate single hypothesis, minimal verification | Confirm hypothesis or make new one |
| **4. Implementation** | Create test (if allowed), fix, verify | Bug fully resolved, system stable |

## When the Process Indicates "No Root Cause"

If systematic investigation indicates the issue is truly environmental, transient, or external:
1. You have completed the process correctly.
2. Document what you investigated.
3. Implement appropriate handling (retries, timeouts, clear error messages).
4. Add logging/monitoring for future investigation.

**But remember:** 95% of "no root cause" cases are actually due to incomplete investigation.

## Supporting Techniques

These techniques are part of systematic debugging and are available in the skills directory:
- **`root-cause-tracing.md`** - Trace errors back through the call stack to find the original trigger.
- **`defense-in-depth.md`** - Add validation checks at multiple layers after finding the root cause.
- **`condition-based-waiting.md`** - Replace arbitrary sleep timeouts with conditional polling.

**Related Skills:**
- **test-driven-development** - To create failing test cases (Phase 4, Step 1) when allowed.
- **verification-before-completion** - Verify the fix works perfectly before declaring completion.

## Real-World Impact

From actual debugging sessions:
- Systematic approach: Takes 15-30 minutes to completely resolve the bug.
- Trial-and-error approach: Takes 2-3 hours spinning around without resolving the issue.
- First-time fix success rate: 95% vs 40%.
- New bugs introduced: Almost zero vs frequently.
