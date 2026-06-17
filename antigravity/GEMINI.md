# GEMINI.md

## 0. Bootstrap — Mandatory Session Initialization
When starting an Antigravity CLI session, the agent MUST perform the following steps **BEFORE** processing any user request:

1. **Load `using-superpowers` skill**: Read the `SKILL.md` of the `using-superpowers` skill (with `IsSkillFile: true`) to activate the skill-first workflow. This is MANDATORY for EVERY conversation — no exceptions.
2. **Discovery Agents**: Scan agent directories in priority order:
   - **Project-level** (high priority): `<project-root>/antigravity/agents/` — project-specific agents
   - **Global-level** (fallback): `~/.gemini/antigravity/agents/` — shared agents across all projects
   - Cache the list of available agents for use with `define_subagent` / `invoke_subagent` as needed.
3. **Discovery Rules**: Scan rule directories in priority order:
   - **Project-level** (high priority): `<project-root>/antigravity/rules/` — project-specific rules
   - **Global-level** (fallback): `~/.gemini/antigravity/rules/` — shared rules across all projects
   - Cache the directory structure (common, language-specific, web) for quick lookup when processing code.
4. **Precedence**: Project-level overrides Global-level when conflicts arise (same agent or rule file name).

> **Note**: This Bootstrap step does NOT replace reading specific rules when processing code (see Section 8). It only builds a "map" for quick lookup.

## 1. Communication & Core Principles
- **Language**: Vietnamese (for all responses, plans, updates, and reports). Use clear, concise, evidence-based wording.
- **Approach**: Fix root causes with simple, small, verifiable changes. Do not expand scope. 
- **Workspace**: Work in the current working directory. No git commits, git worktrees, or isolated workspaces unless explicitly requested.

## 2. Planning & Execution Workflow
- ALWAYS use the `using-superpowers` skill for EVERY task (trivial or non-trivial). This skill is already loaded during Bootstrap (Section 0). Do NOT use the default `/planning` mode.
- **The 5-Step Workflow (Brainstorm -> Approve Spec -> Write Plan -> Approve Plan -> Auto-Execute):**
  1. **Brainstorm/Spec**: Use the `brainstorming` skill to analyze requirements, design a solution. You MUST perform **Dual-Write Sync**: write the Spec file to the project directory (e.g., `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md` with `IsArtifact: false`) and simultaneously save a copy to the Brain directory (e.g., `<artifactDir>/analysis_results.md` with `IsArtifact: true` and `ArtifactMetadata` of type `other`).
  2. **Mandatory Pause 1 (Approve Spec) - HARD GATE**: After presenting the brainstorm/spec, you MUST call the `ask_question` tool to ask the user to approve the Spec (e.g., question: "Approve the Spec to continue?", options: ["Approve Spec and proceed to Plan", "Needs adjustments"]), and output the phrase:
     *"⏸️ **Awaiting Spec approval** — Please respond to the dialog to continue."*
     Then IMMEDIATELY STOP ALL TOOL CALLS. Do not call any tool (including `writing-plans`) until the user explicitly responds with approval.
  3. **Write Plan**: Upon receiving approval for the Spec, AUTOMATICALLY run the `writing-plans` skill to generate a detailed implementation plan. You MUST perform **Dual-Write Sync**: write the Plan file to the project directory (e.g., `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md` with `IsArtifact: false`) and simultaneously save a copy to the Brain directory (e.g., `<artifactDir>/implementation_plan.md` with `IsArtifact: true` and `ArtifactMetadata` of type `implementation_plan`).
  4. **Mandatory Pause 2 (Approve Plan) - HARD GATE**: After writing the implementation plan, you MUST call the `ask_question` tool to ask the user to approve the Plan (e.g., question: "Approve the Plan to continue?", options: ["Approve Plan and execute", "Needs adjustments"]), and output the phrase:
     *"⏸️ **Awaiting Plan approval** — Please respond to the dialog to continue."*
     Then IMMEDIATELY STOP ALL TOOL CALLS. Do not call any tool or transition to execution until the user explicitly responds with approval.
  5. **Auto Execution**: Upon receiving approval for the Plan, AUTOMATICALLY transition directly into `subagent-driven-development` for implementation. DO NOT ask the user to choose an execution mode.
- *Note*: If tests are not requested, plans must explicitly state: *"No new tests will be written; verification will use non-test methods or run existing tests where appropriate."*

## 3. Strict Test-Writing Policy
- **Default Rule**: Do NOT write, create, or modify test code (fixtures, mocks, etc.). "Verify", "check", or "evidence" do not grant permission to write tests.
- **Exceptions**: Only write tests if explicitly requested (e.g., "write tests", "TDD", "add unit tests").
- **Verification**: Prefer build, typecheck, lint, logs, manual inspection, or existing cheap tests.

## 4. Execution Model & Subagents
- **Default Skill**: `subagent-driven-development`.
- **Parallelization**: Prefer batch-parallel execution if logic and state are independent. Otherwise, execute sequentially.
- **Subagent Discovery** (already scanned during Bootstrap — Section 0):
  - Look up agent definitions in order: **project-level** (`<project-root>/antigravity/agents/`) → **global-level** (`~/.gemini/antigravity/agents/`).
  - Before calling `define_subagent`, check if a `<name>.md` file exists in either directory. If found, parse its `description` and `tools` (to enable write/subagent/mcp tools), then invoke it.
  - Project-level agent overrides global-level agent with the same name.
  - If no agent exists, ask the user to create a new definition file or proceed with a temporary one.
- Subagents must have a single objective and strictly follow the no-test-writing policy.

## 5. Scope & Destructive Operations
- **State Isolation**: Treat session data as scope-bound. Verify state isolation before parallel execution.
- **Destructive Actions**: (delete, cleanup, overwrite, truncate, replace) are ONLY permitted if ownership/scope is explicitly proven (e.g., session_id). 
- If ownership is unproven, DO NOT execute destructive operations; report the uncertainty instead.

## 6. Bug Fixes & Refactoring
- **Bug Fixes**: Reproduce the issue and inspect logs/traces before coding. Apply the minimal safe fix.
- **Refactoring**: Must preserve external behavior. Never mix refactor and feature work.

## 7. Definition of Done & Final Report
- **Done Criteria**: Scope addressed, changes verified with evidence, no unauthorized tests created, risks highlighted.
- **Final Report Requirements**:
  1. Summary of modified files.
  2. Verification commands run and their exact results.
  3. A mandatory test statement (if none were requested): *"No new tests were written as the user did not request them."* (Recommendations for missing tests belong here as optional follow-ups only).

## 8. Rules System
- **Rules Directory**: `~/.gemini/antigravity/rules` contains coding guidelines structured hierarchically:
  - `rules/common/` — common rules applicable to ALL languages (coding-style, patterns, security, hooks, testing, code-review, git-workflow, agents, performance, development-workflow)
  - `rules/<language>/` — language-specific rules (typescript, python, dart, golang, java, kotlin, rust, swift, ruby, php, csharp, fsharp, cpp, perl, angular, arkts)
  - `rules/web/` — web/frontend-specific rules (design-quality, performance, security, coding-style, patterns, hooks, testing)
- **Lookup Mechanism**: When operating on a code file, the agent MUST look up the appropriate rules:
  1. Determine the language/domain from the extension of the file being processed (e.g., `.dart` → `dart/`, `.ts` → `typescript/`, `.py` → `python/`)
  2. Read the corresponding rules in `rules/common/` first (baseline)
  3. Read the rules in `rules/<language>/` for additions or overrides (language-specific overrides common)
  4. If working on frontend (HTML/CSS/JS): read `rules/web/` in addition
- **Lookup Timing**:
  - Before writing/modifying code: read the corresponding `coding-style.md`, `patterns.md`, and `security.md`
  - When reviewing code: read `code-review.md` and `security.md`
  - When configuring hooks/CI: read `hooks.md`
  - When designing architecture: read `patterns.md` and `development-workflow.md`
- **Precedence**: Language-specific rules > Common rules (in case of conflicts). User's GEMINI.md rules > Rules files (always takes highest precedence).
- **Note**: Testing/code-review rules are used to CHECK code quality, and MUST NOT be used as a reason to write new tests (adhering to Section 3).