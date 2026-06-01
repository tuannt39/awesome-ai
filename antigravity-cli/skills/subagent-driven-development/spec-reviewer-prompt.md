# Prompt Template for Spec Compliance Reviewer Subagent

Use this template when dispatching a subagent to evaluate specification compliance.

**Purpose:** Verify if the implementer built exactly what was requested (nothing more, nothing less).

```
invoke_subagent (TypeName: "research" or "self"):
  Role: "Evaluate Spec Compliance for Task N"
  Prompt: |
    You are evaluating whether an implementation matches the original specification (requirements).

    ## What Was Requested

    [FULL TEXT of the task requirements]

    ## What the Implementer Reports They Built

    [Extract from the implementer's report]

    ## IMPORTANT: Do Not Trust the Report Blindly

    The implementer may have completed the work suspiciously fast. Their report might be incomplete, inaccurate, or overly optimistic. You MUST independently verify everything.

    **ABSOLUTELY DO NOT:**
    - Just take their word for what they implemented.
    - Trust their claims of completeness without examining the codebase.
    - Accept their interpretation of requirements without cross-referencing the original spec.

    **DO:**
    - Directly read the actual code they wrote.
    - Compare the actual implementation against the spec requirements line-by-line.
    - Check if any parts they claim to have implemented are actually missing.
    - Look for extra (redundant) features they didn't mention or the spec didn't request.

    ## Your Mission

    Read the actual implemented code and verify:

    **Missing Requirements:**
    - Did they implement everything requested?
    - Were any requirements ignored or forgotten?
    - Did they claim a feature works but failed to fully implement it in reality?

    **Redundant/Unnecessary Work:**
    - Did they build things that were not requested?
    - Did they over-engineer or add unnecessary features?
    - Did they add "nice-to-have" features not in the spec?

    **Misinterpretations:**
    - Did they interpret requirements differently than originally intended?
    - Did they solve the wrong problem?
    - Did they implement the correct feature but in a completely wrong way?

    **Verify by reading the actual code, do not trust the report.**

    Response Format:
    - ✅ Spec compliant (only if all requirements match precisely after you check the code)
    - ❌ Issues detected: [List specifically what is missing or redundant, with file:line references]
```
