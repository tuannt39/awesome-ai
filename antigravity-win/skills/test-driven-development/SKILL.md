---
name: test-driven-development
description: Use when implementing any feature or bug fix, before writing the actual implementation code.
---

<ACTIVATION-GATE>
This skill is ONLY allowed to be activated when the user EXPLICITLY REQUESTS writing tests using phrases such as:
"write tests", "TDD", "add unit tests", "test-driven development", "phát triển hướng kiểm thử".

If the user does NOT explicitly request writing tests, the AI ABSOLUTELY MUST NOT write, create, or modify test files (fixtures, mocks, test code, etc.). It is FORBIDDEN to use this skill in that case. Instead, use build, typecheck, lint, logs, and manual testing to verify.
</ACTIVATION-GATE>

# Test-Driven Development (TDD)

## Overview

Write the test first. Observe the test fail. Write the minimum production code to make the test pass.

**Core Principle:** If you have never seen a test fail, you cannot know whether it is actually testing what is required.

**Violating the letter of this rule is violating the spirit of this rule.**

## When to Use

**Always (only when there is an explicit request to write tests from the user):**
- Developing new features
- Bug fixes
- Refactoring code
- Changing system behavior

**Exceptions (to be discussed with your partner):**
- One-off throwaway prototypes
- Automatically generated code
- Configuration files

Thinking of "skipping TDD just this once"? Stop. That is just groundless excuse.

## The Iron Rule

```
DO NOT WRITE PRODUCTION CODE WITHOUT A PRIOR FAILING TEST CASE
```

If you write code before writing the test? Delete it and start over from scratch.

**No exceptions:**
- Do not keep it as a "reference".
- Do not "tweak" it while writing the test.
- Do not look back at it.
- Deleting means deleting completely.

Implement completely fresh based on the test cases. Period.

## Red-Green-Refactor Cycle

```
[RED: Write a failing test] ---> [Verify the test fails correctly]
                                         | (correct)
                                         v
[REFACTOR: Clean up code] <--- [Verify the test passes (GREEN)] <--- [GREEN: Write minimum code]
```

### RED - Write a Failing Test

Write the smallest test case that demonstrates the desired system behavior.

<Good>
```typescript
test('retries failed operations up to 3 times', async () => {
  let attempts = 0;
  const operation = () => {
    attempts++;
    if (attempts < 3) throw new Error('failed');
    return 'success';
  };

  const result = await retryOperation(operation);

  expect(result).toBe('success');
  expect(attempts).toBe(3);
});
```
Clear name, tests actual behavior, focuses on a single responsibility.
</Good>

<Bad>
```typescript
test('retrying works well', async () => {
  const mock = jest.fn()
    .mockRejectedValueOnce(new Error())
    .mockRejectedValueOnce(new Error())
    .mockResolvedValueOnce('success');
  await retryOperation(mock);
  expect(mock).toHaveBeenCalledTimes(3);
});
```
Vague name, tests mocks instead of the actual code.
</Bad>

**Mandatory Requirements:**
- Test only a single behavior at a time.
- Clear test names.
- Use actual code (limit mocking unless absolutely necessary).

### Verify RED - Observe the Test Fail

**MANDATORY. Never skip this.**

```bash
# Example running test on Windows PowerShell
npm test path/to/test.test.ts
```

Confirm:
- The test fails (not a compiler syntax error).
- The error message is as expected.
- The failure is due to the feature not being implemented (not a typo).

**Test passes immediately?** You are testing existing behavior. Rewrite the test.
**Test hits a system error?** Fix it and run again until the test fails correctly.

### GREEN - Write the Minimum Code

Write the simplest code possible to make the test case pass.

<Good>
```typescript
async function retryOperation<T>(fn: () => Promise<T>): Promise<T> {
  for (let i = 0; i < 3; i++) {
    try {
      return await fn();
    } catch (e) {
      if (i === 2) throw e;
    }
  }
  throw new Error('unreachable');
}
```
Just enough to pass the test.
</Good>

<Bad>
```typescript
async function retryOperation<T>(
  fn: () => Promise<T>,
  options?: {
    maxRetries?: number;
    backoff?: 'linear' | 'exponential';
    onRetry?: (attempt: number) => void;
  }
): Promise<T> {
  // YAGNI - You don't need these complex features yet!
}
```
Over-engineered design, redundant functionality.
</Bad>

Do not add features that are not yet needed, do not seize the opportunity to refactor unrelated code, do not optimize beyond the scope of the current test case.

### Verify GREEN - Observe the Test Pass

**MANDATORY.**

```bash
npm test path/to/test.test.ts
```

Confirm:
- The test passes successfully.
- All other tests still pass.
- Clean output (no redundant warnings or errors).

**Test still fails?** Fix the implementation code, do not modify the test.
**Other tests broke?** Fix the regression immediately.

### REFACTOR - Clean Up Code

Perform only after all tests have turned green:
- Eliminate duplicate code.
- Improve variable/function naming.
- Extract helper functions.

Always ensure tests remain green. Do not add new behavior during this phase.

### Repeat the Process

Continue writing a failing test for the next feature.

## What Makes a Good Test

| Attribute | Good | Bad |
| :--- | :--- | :--- |
| **Minimal** | Focuses on one thing. Name contains "and"? Split it. | `test('validate email and domain and whitespace')` |
| **Clear** | Name describes exactly the expected behavior | `test('test 1')` |
| **Expresses Intent** | Illustrates clearly how to use the expected API | Obscures or makes code behavior hard to understand |

## Why the Order Matters

**"I will write tests after writing code to verify"**

Tests written after code often pass immediately. This proves nothing:
- You might be testing the wrong thing.
- You might be testing implementation details instead of behavior.
- You easily overlook edge cases you forgot when writing the code.
- You have never seen that test actually catch an error.

Writing tests first forces you to see them fail, proving the test actually works and has diagnostic value.

**"I have manually tested all edge cases already"**

Manual testing is spontaneous and ad-hoc. You think you tried everything, but:
- There is no record of what you tested.
- It cannot be re-run automatically when code changes.
- Under time pressure, you easily forget edge cases.
- "Works on my machine" does not mean the system is stable.

Automated tests are systematic. They run identically every time, everywhere.

**"Deleting hours of work is too wasteful"**

This is the sunk cost fallacy. That time is already gone. Your current choices are:
- Delete it and rewrite using TDD (takes a little more time, but gives absolute confidence).
- Keep it and write tests later (takes 30 minutes, but lacks confidence and is prone to hidden bugs).

The real waste is keeping code you cannot trust 100%. Code that runs without real tests is serious technical debt.

**"TDD is too dogmatic, reality requires flexibility"**

TDD is highly practical:
- Detects bugs before committing (much faster than debugging later).
- Prevents regressions.
- Serves as living documentation for the codebase.
- Allows confident refactoring without breaking the system.

"Flexible" skipping of tests = debugging directly in production = slow and risky.

## Common Excuses

| Excuse | Reality |
| :--- | :--- |
| "The code is too simple to need tests" | Simple code can still break. Writing a test takes only 30 seconds. |
| "I will write tests later" | Tests passing immediately prove nothing. |
| "Writing tests later achieves the same goal" | Writing tests later answers: "What is the code doing?". Writing tests first answers: "What should the code do?". |
| "Already manually tested it" | Manual testing is not systematic, has no record, and cannot be re-run automatically. |
| "Deleting code is wasteful" | Sunk cost fallacy. Keeping code of unknown quality invites technical debt. |
| "Keep it for reference, write test first" | You will definitely find a way to keep the old logic. That is writing tests later. Delete means delete completely. |
| "Need to prototype first to find a solution" | Totally fine. But once you find the solution, throw the prototype away and start over using TDD. |
| "Test is too hard to write = design is unclear" | Listen to the test. Hard-to-test code means it is tightly coupled and hard to use. |
| "TDD slows me down" | TDD is always faster than debugging the system later. |

## Red Flags - STOP and Start Over

- Writing code before writing tests.
- Writing tests after the implementation code is done.
- Tests passing immediately on the first run.
- Unable to explain why the test failed.
- Promising to write tests "later".
- Excusing "just this once".
- "I verified it manually".
- "Keeping old code for reference".

**ALL the above signs mean: DELETE CODE. Start over with TDD.**

## TDD Verification Checklist

Before claiming work is done:

- [ ] Every new function/method has an accompanying test.
- [ ] Directly observed each test fail before writing the implementation code.
- [ ] Each test failed for the correct reason (feature missing, not syntax error).
- [ ] Only wrote the minimum code to make the test pass.
- [ ] All tests pass successfully.
- [ ] Clean output, no errors or redundant warnings.
- [ ] Tests use actual code (mocking minimized).
- [ ] Adequately covers edge cases and error cases.

If you haven't checked all the boxes above, you skipped TDD. Delete and start over.
