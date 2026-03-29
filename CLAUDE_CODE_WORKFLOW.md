# Claude Code Workflow
### Generic workflow for building any product and adding features to existing projects
### Works for any language — Python, TypeScript, Go, Java, Rust, Ruby, PHP, Swift, Kotlin

---

## Table of Contents

- [One-Time Global Setup](#one-time-global-setup)
- [Language Command Reference](#language-command-reference)
- [Project Setup](#project-setup-do-once-per-project)
- [CLAUDE.md Template](#claudemd-template)
- [settings.json Template](#settingsjson-template)
- [Commands](#commands-copy-into-claude-commands)
- [Agents](#agents-copy-into-claude-agents)
- [Workflow A — Building a New Product](#workflow-a--building-a-new-product)
- [Workflow B — Adding a Feature to Existing Project](#workflow-b--adding-a-feature-to-existing-project)
- [The Four Phase Model](#the-four-phase-model-explained)
- [When to Use Parallel Workers](#when-to-use-parallel-workers)
- [Context Management](#context-management)
- [Quick Reference Card](#quick-reference-card)

---

## One-Time Global Setup

Install these once. They work across all your projects.

```bash
# Superpowers — brainstorming, TDD, code review, branch management
/plugin install superpowers@claude-plugins-official

# Everything Claude Code — 28 agents, 125 skills, 60 commands
/plugin marketplace add affaan-m/everything-claude-code
/plugin install everything-claude-code@everything-claude-code

# Boris Cherny tips as a reference skill
mkdir -p ~/.claude/commands
curl -L -o ~/.claude/commands/boris-cherny.md \
  https://raw.githubusercontent.com/arjangirijobs/boris-cherny-claude-code/main/SKILL.md
```

---

## Language Command Reference

Replace `[BUILD]`, `[TEST]`, `[LINT]`, `[FORMAT]` throughout this workflow with your language commands.

| Language | Build | Test | Lint | Format |
|---|---|---|---|---|
| TypeScript / Node | `npm run build` | `npm test` | `npm run lint` | `npm run format` |
| Python | `python -m build` | `pytest` | `ruff check .` | `ruff format .` |
| Go | `go build ./...` | `go test ./...` | `golangci-lint run` | `gofmt -w .` |
| Java | `mvn package` | `mvn test` | `mvn checkstyle:check` | `mvn spotless:apply` |
| Rust | `cargo build` | `cargo test` | `cargo clippy` | `cargo fmt` |
| Ruby | `bundle exec rake` | `bundle exec rspec` | `rubocop` | `rubocop -a` |
| PHP | `composer install` | `./vendor/bin/phpunit` | `./vendor/bin/phpcs` | `./vendor/bin/phpcbf` |
| Swift | `swift build` | `swift test` | `swiftlint` | `swiftformat .` |
| Kotlin | `./gradlew build` | `./gradlew test` | `./gradlew ktlintCheck` | `./gradlew ktlintFormat` |

---

## Project Setup — Do Once Per Project

```bash
# Create project
mkdir my-project && cd my-project
git init

# Create folder structure
mkdir -p .claude/commands .claude/agents .claude/skills .claude/rules
mkdir -p docs tasks src tests

# Create all config files (templates below)
# CLAUDE.md
# .claude/settings.json
# .claude/commands/*.md  (7 files)
# .claude/agents/*.md    (5 files)

# First commit — clean baseline
git add .
git commit -m "chore: project setup with Claude Code configuration"

# Open Claude Code
claude
```

---

## CLAUDE.md Template

Copy this into your project root. Fill in your stack details.

```markdown
# [Project name]
[One sentence: what this project does and who uses it]

# Commands — any developer runs these on first try
Build:  [YOUR BUILD COMMAND]
Test:   [YOUR TEST COMMAND]
Lint:   [YOUR LINT COMMAND]
Format: [YOUR FORMAT COMMAND]
Run:    [YOUR DEV/RUN COMMAND]

# Stack
Language:        [Python / TypeScript / Go / Java / Rust / etc]
Framework:       [Django / Next.js / Gin / Spring / Actix / etc]
Database:        [Postgres / MySQL / MongoDB / SQLite / etc]
Package manager: [pip / npm / go mod / maven / cargo / etc]

# Behaviour — always enforced
- Use Plan Mode (Shift+Tab) for any task touching 3 or more files
- Never push directly to the main branch
- Never skip or delete existing tests
- Never mark a task complete without running tests first
- Only change files listed in the approved plan
- Stop and report immediately if something unexpected appears
- Commit after every completed task

# Code — always enforced
- Match the existing code style in every file touched
- Never introduce patterns not already in this codebase
- All config via environment variables — nothing hardcoded
- Max 30 lines per function — split if longer
- Every public function needs a docstring or equivalent comment

# Error handling — always enforced
- Use named error types — never throw or raise generic exceptions
- Log on every error path: level, timestamp, module, operation, error

# Testing — always enforced
- Run tests after every file change
- Zero regressions — fix implementation not tests
- New behaviour always needs a new test
- Minimum 80% coverage

# Context management — always enforced
- At 50% context: /compact — preserve list of modified files and test status
- At 90% context: /clear — start fresh session, paste current task again

# After any correction
- Add to tasks/lessons.md: what went wrong + rule that prevents it next time
- Suggest CLAUDE.md additions if the rule should apply permanently

# Imports
# @import .claude/rules/coding-style.md
# @import .claude/rules/git-workflow.md
# @import .claude/rules/testing.md
# @import tasks/lessons.md
```

---

## settings.json Template

Copy into `.claude/settings.json`. Replace `[FORMAT]` and `[TEST]` with your commands.

```json
{
  "model": "sonnet",
  "env": {
    "MAX_THINKING_TOKENS": "10000",
    "CLAUDE_AUTOCOMPACT_PCT_OVERRIDE": "50",
    "CLAUDE_CODE_SUBAGENT_MODEL": "sonnet"
  },
  "permissions": {
    "allow": [
      "Bash([BUILD]:*)",
      "Bash([TEST]:*)",
      "Bash([LINT]:*)",
      "Bash([FORMAT]:*)",
      "Bash(git status:*)",
      "Bash(git diff:*)",
      "Bash(git log:*)",
      "Bash(git add:*)",
      "Bash(git commit:*)",
      "Bash(find:*)",
      "Bash(grep:*)",
      "Bash(cat:*)"
    ],
    "deny": [
      "Bash(git push --force:*)",
      "Bash(rm -rf:*)",
      "Bash(git push origin main:*)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "[FORMAT] || true"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "[TEST] 2>&1 | tail -3"
          }
        ]
      }
    ]
  }
}
```

---

## Commands — Copy into `.claude/commands/`

### `plan.md`

```markdown
Read docs/requirements.md and docs/architecture.md if they exist.
Read the existing codebase structure using grep and glob.

Write a complete phased implementation plan into docs/plan.md

Organise into exactly 4 phases:
  Phase 1: Foundation
    config, shared types, base errors, logger, db schema, test setup
  Phase 2: Core
    identity module + any module everything else depends on
  Phase 3: Independent features
    one task per feature module, each depends only on Phase 1 and 2
    label each task with: PARALLEL-SAFE
  Phase 4: Integration
    wire modules together, end to end flows, security, deploy prep

For every task include:
- Exact file paths to create or change
- What the change does and why
- The failing test to write first (TDD)
- How to verify it works

Tasks must be 2-5 minutes each.
Do not implement. Write the plan only and stop.
```

### `implement.md`

```markdown
Read docs/plan.md

Execute the approved plan exactly as written.

Rules:
- Change only files listed in the plan
- Match the existing code style in every file
- Run the build command after each file — fix errors before the next file
- Write the failing test before every implementation (TDD)
- Stop and report immediately if anything unexpected appears
- Never refactor anything outside the current task scope

After each task: say what you built and what is next.
Stop after completing each phase. Wait for me to say continue.
```

### `review.md`

```markdown
Read all files changed in the current task.

Review as a principal engineer preparing for a production incident.

For each issue found:
- File name and line number
- Risk: Critical / High / Medium / Low
- What is wrong
- The exact fix

Check for:
- Bugs and off-by-one errors
- Missing error handling paths
- Security: unvalidated input, exposed data, secrets in logs
- Violations of existing codebase patterns
- Files touched that were not in the approved plan
- Missing or inconsistent logging
- SOLID violations — name the principle broken

Review changed files only. Do not comment on unrelated code.
Report all issues. Do not fix anything yet. Wait for my instruction.
```

### `test.md`

```markdown
Read all recently changed files.

Generate tests covering:
1. New behaviour — happy path and every failure path
2. Edge cases: null/nil/None, empty, max values, zero, concurrent calls
3. One regression test that permanently locks this feature in

Use the same test framework already in this project.
Follow the existing test file naming and folder structure exactly.
Run all tests — old and new together.
Report: X passing, 0 failing, coverage percentage.
```

### `fix-issue.md`

```markdown
Issue: $ARGUMENTS

Read the relevant source files first.
Find the root cause — do not guess.

Then:
1. Write a failing test that reproduces the bug exactly
2. Fix the implementation until that test passes
3. Confirm no existing tests broke
4. Add to tasks/lessons.md: root cause + rule that prevents it
```

### `checkpoint.md`

```markdown
Save current state to tasks/current-task.md:
- Every file modified so far in this session
- Every file created so far in this session
- Current test status: X passing, Y failing
- Current phase and what tasks remain
- Any decisions made that are not captured in the plan
```

### `learn.md`

```markdown
Review this session.

Extract:
- Mistakes that were made and corrected
- Patterns that worked well
- What took longer than expected and why

Write findings to tasks/lessons.md in this format:
  Mistake: [what happened]
  Rule: [what prevents it next time]

Then suggest specific additions to CLAUDE.md based on these findings.
Show me the exact lines to add.
```

---

## Agents — Copy into `.claude/agents/`

### `planner.md`

```markdown
---
name: planner
description: Creates detailed phased implementation plans before any code is written
model: opus
effort: high
tools: Read, Grep, Glob, Write
---
You are a staff engineer creating an implementation plan.

Read the codebase using grep and glob to understand existing patterns.
Write a plan specific enough for a junior developer to follow without guessing.

Every task must have:
- Exact file paths
- Specific changes described clearly
- The failing test to write first
- Verification steps

Break tasks into 2-5 minute chunks.
Order by dependency — prerequisites always first.
Flag every risk and ambiguity explicitly.
Label Phase 3 tasks as PARALLEL-SAFE only if they have no cross-dependencies.
Do not write any implementation code.
```

### `code-reviewer.md`

```markdown
---
name: code-reviewer
description: Reviews code as a principal engineer for bugs, security, and quality
model: opus
effort: high
tools: Read, Grep, Glob
---
You are a principal engineer reviewing code before it reaches production.

Read only the changed files. Never comment on unrelated code.

For every issue:
- File name and exact line number
- Risk: Critical / High / Medium / Low
- What is wrong and why it matters in production
- The exact fix required

Check: bugs, missing error handling, security gaps,
violations of existing codebase patterns, missing tests,
inconsistent logging, anything outside the approved change scope.

Be critical. Politeness hides risk.
Never say "looks good" without evidence.
```

### `security-reviewer.md`

```markdown
---
name: security-reviewer
description: Reviews code for security vulnerabilities across any language
model: opus
effort: high
tools: Read, Grep, Glob
---
You are a senior security engineer.

Review for:
- Injection vulnerabilities: SQL, command, path traversal, template injection
- Authentication and authorisation flaws
- Sensitive data in logs, error messages, or API responses
- Unvalidated or unsanitised input at every public boundary
- Hardcoded secrets, API keys, tokens, or passwords
- Insecure dependencies or known vulnerable patterns
- Missing rate limiting on sensitive endpoints
- Insecure direct object references

Provide file name, line number, severity, and exact fix for every finding.
Severity: Critical / High / Medium / Low
```

### `test-writer.md`

```markdown
---
name: test-writer
description: Writes comprehensive tests for any module in any language
model: sonnet
tools: Read, Write, Bash
---
You are a senior QA engineer.

For any module given:
1. Read all files in the module
2. Write unit tests for every public function — every branch
3. Write integration tests for every external call
4. Write adversarial tests — what inputs would break this?
5. Write one permanent regression test for this feature
6. Run the full test suite and fix any setup issues
7. Report final coverage percentage

Use the test framework already present in this project.
Follow existing test naming and folder conventions exactly.
Never delete or modify existing tests.
```

### `verify-app.md`

```markdown
---
name: verify-app
description: Verifies the application is production ready after any change
model: sonnet
tools: Read, Bash
---
You are a QA engineer doing a pre-release verification.

Run every check in this exact order:
1. Build — must pass with zero errors
2. Tests — must pass with zero failures
3. Lint — must pass with zero errors
4. No hardcoded secrets or credentials in changed files
5. No debug print/log statements left in production code
6. No TODO or FIXME comments left in changed files
7. All environment variables in .env.example are documented

Report pass or fail for each check with specific error details on failures.
Final verdict: READY or NOT READY

Never report READY if any check failed.
```

---

## Workflow A — Building a New Product

### Before opening Claude Code

```bash
# Set up the project (see Project Setup above)
# Then open Claude Code
claude
```

---

### Step 1 — Brainstorm and spec

Press `Shift+Tab` to enter **Plan Mode**. Then type:

```
I want to build: [describe your idea in plain language — no special format needed]

Ask me questions using the AskUserQuestion tool until you understand:
- Who the users are and what they need
- The core actions the system must support
- What is explicitly out of scope in version 1
- Any hard constraints (compliance, performance, tech stack)
- How success will be measured

Do not write any code or files yet. Interview me first.
```

Answer every question Claude asks. After the interview:

```
Based on our conversation write a complete spec to docs/requirements.md

Include:
- Functional requirements — numbered, testable, written as "The system SHALL..."
- Non-functional requirements — with specific targets for latency, availability, throughput
- Out of scope — explicitly listed to prevent scope creep
- Success metrics
- Open decisions that need an ADR

Then propose the architecture. Identify and label each module:
- Phase 1 Foundation: config, types, errors, logger, db, test setup
- Phase 2 Core: identity + the 1-2 modules every feature will need
  (ask yourself: what does almost every feature in this product need?)
- Phase 3 Independent features: what users actually do in this app
  (confirm each one only depends on Phase 1 and 2, not on each other)
- Phase 4 Integration: wiring, end to end flows, security, deploy

Do not implement. Spec and architecture only.
```

Read `docs/requirements.md`. Fix anything wrong. Add anything missing.

---

### Step 2 — Write the phased plan

```
/plan
```

Press `Ctrl+G` to open `docs/plan.md` in your editor.

Read through the plan and add your own notes directly into the file. Lines starting with `> NOTE:` are your corrections. Example:

```markdown
## Task 5: Create user repository

> NOTE: use ORM — no raw SQL anywhere
> NOTE: soft delete with deleted_at — never hard delete user records

## Task 9: Login endpoint

> NOTE: POST not GET
> NOTE: rate limit to 10 requests per minute per IP
> NOTE: never log the password in any form, not even hashed
```

Save the file, then send back with the guard phrase — never skip this:

```
Address all notes in docs/plan.md
Do not implement yet. Show me the updated plan.
```

Repeat annotating and sending back until the plan has zero ambiguity. Only then move forward.

---

### Step 3 — Challenge the plan with a second Claude

Open a **second terminal** in the same project:

```bash
claude
```

In this second session:

```
Read docs/plan.md and docs/requirements.md

Review this plan as a principal engineer.

Find:
- Tasks in the wrong order — dependency violations
- Missing tasks that will cause failures later
- Tasks too vague to implement without guessing
- Security gaps
- Missing tests

Most importantly: verify Phase 3 modules are truly independent.
For each Phase 3 module: does it depend on any other Phase 3 module?
If yes — flag it. It should move to Phase 4 integration.

Severity: Critical / High / Medium
Be brutal. This plan controls everything.
```

Update `docs/plan.md` in the main session based on the feedback. Close the second terminal.

---

### Step 4 — Phase 1: Foundation (single Claude)

Press `Shift+Tab` — back to **Normal Mode**. Start in an isolated worktree:

```bash
claude --worktree
```

```
/implement

Execute Phase 1 tasks only.
When Phase 1 is complete — stop and report.
Do not start Phase 2.
```

When complete:

```
/checkpoint
```

```
Use the verify-app subagent for Phase 1 check.

Verify:
- Build passes with zero errors
- All Phase 1 tests pass
- Shared types are defined and exported correctly
- Logger works and outputs structured format
- Database connection works
- Config loads correctly for all environments
- Test harness is running

Report READY or NOT READY with specific details.
Do not proceed to Phase 2 until READY.
```

> **Do not move to Phase 2 until verify-app reports READY.**

---

### Step 5 — Phase 2: Core (single Claude)

```
/implement

Execute Phase 2 tasks only.
When Phase 2 is complete — stop and report.
Do not start Phase 3.
```

When complete:

```
/checkpoint
```

```
Use the verify-app subagent for Phase 2 check.

Verify:
- All Phase 2 modules are fully tested
- Identity works end to end: create account, login, authenticate, authorise
- All core module interfaces are stable and correctly exported
- Any Phase 3 module can safely import from Phase 1 and Phase 2

Report READY or NOT READY with specific details.
```

> **This is the most critical checkpoint in the entire workflow.**
> If Phase 2 has any cracks, parallel workers will build on broken foundations.
> Take as long as needed here. Fix everything before proceeding.

---

### Step 6 — Phase 3: Independent features (parallel workers)

**First — confirm independence before opening any extra terminals:**

```
Read docs/plan.md Phase 3.

List every Phase 3 module.
For each one confirm:
- It imports from Phase 1 and Phase 2 only
- It does not import from any other Phase 3 module
- It writes to unique files no other Phase 3 module touches

If any module depends on another Phase 3 module
move it to Phase 4 integration instead.

Give me the confirmed final list of independent Phase 3 modules.
```

**Open one terminal per confirmed independent module:**

```bash
# One terminal per module — give each a descriptive name
claude --worktree --name "worker-[module-name]"
```

**In each worker terminal — use this generic prompt:**

```
You are implementing [ModuleName] only.

Before starting read:
- docs/plan.md — your tasks are in Phase 3 under [ModuleName]
- src/shared/types/ — shared types you must use, do not redefine them
- src/shared/errors/ — base error classes you must extend
- src/shared/logging/ — logger you must use
- src/modules/[core-module]/ — the core module interface you will call

Your rules:
- Implement [ModuleName] tasks only
- Do not create or modify files outside your module folder
- Do not import from any other Phase 3 module
- Follow TDD — write the failing test before every implementation
- Run the full test suite after each task
- If you need something from another Phase 3 module:
  stop immediately and report: BLOCKED — [what you need and why]

When all your tasks are complete report: DONE
```

**While workers are running — your job as the human:**

Monitor all terminals. When a worker reports `DONE`:

```
/review
```

Fix Critical and High issues:

```
Fix all Critical and High issues from the review.
Do not touch files outside this module.
Run all tests after every fix.
Confirm still DONE.
```

When a worker reports `BLOCKED`:

```
Stop. What exactly do you need?
Describe the interface you need from [other module].
```

Then in the main terminal, add that interface to Phase 1 shared types and have the worker continue.

**When all workers are DONE and reviewed — back in main terminal:**

```
All Phase 3 workers are complete and reviewed.

Merge all Phase 3 worktrees into the main branch.
Run the full test suite across all modules together.
Report any merge conflicts or cross-module test failures.
```

---

### Step 7 — Phase 4: Integration (single Claude)

Back to single Claude in the main terminal:

```
/implement

Execute Phase 4 tasks only.
Wire all modules together.
Build the end to end flows.
Run the full integration test suite.
Stop when Phase 4 is complete.
```

When complete:

```
/checkpoint
```

```
Use the verify-app subagent for full system check.

Verify:
- Build passes with zero errors
- All tests pass across every module
- At least one end to end flow works completely
- No circular imports anywhere
- No module calls another module in a way not in the plan

Report READY or NOT READY.
```

---

### Step 8 — Quality, security, review

```
/simplify
```

```
Use the security-reviewer subagent on src/
```

```
/review
```

Fix all Critical and High issues. Run the full test suite after every fix.

---

### Step 9 — Learn and ship

```
/learn
```

```
Based on tasks/lessons.md suggest specific additions to CLAUDE.md
that would prevent these issues in future sessions.
Show me the exact lines to add.
```

Update `CLAUDE.md`. Every correction compounds permanently.

```bash
git add .
git commit -m "feat: complete [product name] initial build"
git push
```

---

## Workflow B — Adding a Feature to Existing Project

### Before opening Claude Code

```bash
# Always create a branch first — never work on main
git checkout -b feature/<name>

# Verify clean baseline
git status          # must show nothing uncommitted
[TEST COMMAND]      # must be fully green

# If tests are failing before you touch anything:
# Fix the baseline first. Never start on broken ground.
```

---

### Step 1 — Map the existing codebase

```bash
claude
```

```
Read the src/ folder using grep and glob.

Map what exists:
- Every module and its single responsibility
- Naming conventions used consistently throughout
- Error handling pattern in use
- Logging format and which logger is used
- How database calls are structured
- How external services are called
- Test structure and current coverage

Do not suggest any changes. Map only.
```

Read the map. Correct anything Claude got wrong about your project.

---

### Step 2 — Impact analysis

```
I want to add this feature:
[describe the feature in plain language]

Based on what you just read, answer these questions
without touching any files:

1. Which files need to change and exactly why?
2. Which files are affected indirectly?
3. Which existing tests cover the code being changed?
4. Does this need a database migration? If yes, what changes?
5. Does this change any API request or response contracts?
6. What is the riskiest part of this change?
7. Is there any tech debt that makes this harder than it should be?

Analysis only. No solutions yet.
```

Read carefully. If the impact is larger than expected, adjust the scope of the feature before proceeding.

---

### Step 3 — Plan and approve

Press `Shift+Tab` — **Plan Mode**:

```
/plan
```

Press `Ctrl+G` to open `docs/plan.md`. Annotate every decision you want to influence:

```markdown
## Change 3: Update user service

> NOTE: validate all input before it reaches the database
> NOTE: do not allow email changes here — that is a separate flow
> NOTE: log the old and new values on every update for audit trail
```

Send back with the guard phrase:

```
Address all notes in docs/plan.md
Do not implement yet. Show me the updated plan.
```

Approve only when zero ambiguity remains in the plan.

---

### Step 4 — Implement

Press `Shift+Tab` — **Normal Mode**:

```
/implement
```

**The one rule above everything else:** if Claude touches any file not in the approved plan, stop it immediately:

```
Esc Esc
```

Then:

```
Stop. [filename] is not in the approved plan.
Revert that change.
Only change files listed in docs/plan.md
```

---

### Step 5 — Zero regressions

```
Run all existing tests for the modules that were changed.

For every test that now fails:
- Show the exact error message
- Is this a real regression caused by our change?
  Or does the test need updating because the behaviour intentionally changed?

If real regression: fix the implementation — not the test.
If legitimate change: update the test with a comment explaining
  why the expected behaviour changed.

Never skip, mark as pending, or delete any existing test.
Zero failures required before proceeding.
```

If you cannot reach zero failures — go back to Step 4 and fix the implementation. Do not proceed on broken ground.

---

### Step 6 — New tests

```
/test
```

---

### Step 7 — Review, fix, quality, security

```
/review
```

Fix Critical and High issues:

```
Fix all Critical and High issues from the review.
Do not touch any files outside those already changed.
Run all tests after every fix.
```

Quality pass:

```
/simplify
```

Security check on changed files only:

```
Use the security-reviewer subagent on these specific files:
[list the files that changed]
```

---

### Step 8 — Final check and commit

```
Give me the final summary before I commit:
1. Every file changed — list them
2. Every new test added — list them
3. All tests passing — confirm with count
4. No tests deleted or skipped — confirm
5. Scope stayed within the approved plan — confirm
6. Anything I should manually verify in the running application?
```

Read the summary. Investigate anything unexpected before committing.

**You commit manually — Claude never commits for you:**

```bash
git add .
git commit -m "[type]([module]): [what changed and why]

- [specific change 1]
- [specific change 2]
- migration: [schema change if applicable]

Closes #[issue number]"

git push origin feature/<name>
```

Commit types: `feat`, `fix`, `refactor`, `test`, `chore`, `docs`

---

### Step 9 — Learn

```
/learn
```

Update `CLAUDE.md` with any new rules.

---

## The Four Phase Model Explained

Every product regardless of what it does follows exactly four phases. The names never change. Only the content inside each phase changes.

```
Phase 1 — Foundation (always the same across all products)
  What goes here:
  - Project structure and folder layout
  - Config and environment variable loading
  - Shared types and interfaces
  - Base error classes
  - Structured logger
  - Database connection and schema
  - Test harness setup

  Why single Claude: everything in Phases 2, 3, and 4 depends
  on these existing. Workers cannot share interfaces that do not exist.

Phase 2 — Core (changes per product — find yours with the question below)
  What goes here:
  - Identity module — whoever your users or actors are
  - The 1-2 modules that almost every feature will call

  How to find your Phase 2 core:
  Ask: "What does almost every feature in my product need to know about?"

  Examples by product type:
  E-commerce        → UserModule + ProductCatalogModule
  Blog platform     → UserModule + PostModule
  Chat application  → UserModule + ConversationModule
  Data pipeline     → SourceConnectorModule + SchemaModule
  CLI tool          → ConfigModule + CommandRouterModule
  IoT platform      → DeviceModule + EventStreamModule
  Task manager      → UserModule + WorkspaceModule

  Why single Claude: these modules depend on each other.
  Auth depends on users. Users depend on shared types.
  Cannot be parallelised.

Phase 3 — Independent features (parallel workers)
  What goes here:
  - All the feature modules specific to your product
  - Each one answers: "What can users do in this app?"
  - Each only depends on Phase 1 and Phase 2

  How to identify them:
  Ask: "What are the main things users do in this app?"
  Each distinct action becomes one Phase 3 module.

  Examples by product type:
  E-commerce        → CartModule, OrderModule, PaymentModule,
                      SearchModule, ReviewModule, NotifModule
  Blog platform     → CommentModule, TagModule, SearchModule,
                      SubscriptionModule, AnalyticsModule
  Task manager      → TaskModule, LabelModule, AssignmentModule,
                      CommentModule, NotifModule
  Data pipeline     → TransformModule, ValidatorModule,
                      OutputModule, MonitorModule
  CLI tool          → one module per subcommand

  Why parallel: these are genuinely independent.
  Each worker can build without knowing what others are building.

Phase 4 — Integration (always the same across all products)
  What goes here:
  - Wire all modules together
  - End to end flows
  - Full system test suite
  - Security review
  - Performance checks
  - Deploy preparation

  Why single Claude: needs to see the whole picture to connect it.
```

---

## When to Use Parallel Workers

### The decision rule — answer before opening extra terminals

```
Ask yourself: "Do any of my Phase 3 modules depend on each other?"

Yes → single Claude, sequential
No  → parallel workers, one terminal per module
```

Or ask Claude directly:

```
Are these Phase 3 modules truly independent?
Does any one of them import from or depend on another?
Confirm for each module before I open parallel terminals.
```

### When NOT to use parallel workers

```
Adding a single feature to an existing app  → single Claude
Fixing a bug                                → single Claude
Phase 1 and Phase 2 work                    → single Claude, always
Modules that share output files             → single Claude
Modules where one calls the other           → single Claude
Small tasks under 30 minutes each           → single Claude is faster
```

### When to use parallel workers

```
Phase 3 of a new product build              → parallel workers
Large independent migrations across files   → parallel workers
Multiple truly independent subcommands      → parallel workers
Parallel review + parallel build            → parallel workers
```

### The timing rule

```
Single Claude → Phase 1 Foundation  (solid verified foundation)
Single Claude → Phase 2 Core        (solid verified core)
                                     ↑
                      most critical checkpoint
                      do not proceed until READY
                                     ↓
Parallel workers → Phase 3 Features  (independent modules)
Single Claude → Phase 4 Integration  (connect everything)
```

---

## Context Management

| Context level | What to do |
|---|---|
| 0–50% | Work freely |
| 50% | `/compact` — say "preserve: modified files list and test status" |
| 70% | `/compact` immediately — do not wait |
| 90%+ | `/clear` — start fresh session, paste `docs/plan.md` and current task again |

```bash
Esc Esc   # Undo last agent action (checkpoint rewind)
/rewind   # Roll back to a previous checkpoint
```

### Model selection

```bash
# Default in settings.json: sonnet — handles 80% of all tasks

/model opus     # Switch for: plan review, architecture, complex debugging
ultrathink      # Add to any prompt for deep extended reasoning
/model sonnet   # Switch back as soon as planning is complete
```

---

## Quick Reference Card

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WORKFLOW A — NEW PRODUCT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Shift+Tab                    Enter Plan Mode
Brainstorm                   AskUserQuestion interview — no special format needed
/plan                        4-phase plan written to docs/plan.md
Ctrl+G + annotate            Open plan.md, add > NOTE: lines directly
Guard phrase                 "address all notes, don't implement yet"
Second terminal              "review plan as principal engineer"
Shift+Tab                    Back to Normal Mode
claude --worktree            Isolated branch for all work

Phase 1 Foundation           /implement — Phase 1 only — stop after
/checkpoint                  verify-app → READY before Phase 2

Phase 2 Core                 /implement — Phase 2 only — stop after
/checkpoint                  verify-app → READY ← most critical checkpoint

Phase 3 Features             PARALLEL — one terminal per independent module
  claude --worktree --name "worker-[name]"  one per module
  Prompt: read plan + shared types + implement own module only
  TDD: test first, then code
  Each reports: DONE or BLOCKED
  You: /review each worker when DONE
  Main terminal: merge all worktrees when all DONE

Phase 4 Integration          /implement — Phase 4 only — stop after
/checkpoint                  verify-app → full system READY

/simplify                    Quality pass
security-reviewer subagent   Full codebase security audit
/review                      Staff engineer review of everything
Fix Critical and High        Run full tests after every fix
/learn                       Extract session patterns
Update CLAUDE.md             Every correction is permanent
git commit                   You commit manually — never Claude

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WORKFLOW B — SINGLE FEATURE CHANGE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
git checkout -b feature/name     Branch first — always, no exceptions
[TEST COMMAND]                   Clean baseline — must be fully green

Map codebase                     "read src/ using grep and glob, map only"
Impact analysis                  Which files, which tests, any migration, risks
Shift+Tab                        Plan Mode
/plan                            Feature plan written to docs/plan.md
Ctrl+G + annotate                Add > NOTE: lines to plan.md directly
Guard phrase                     "address all notes, don't implement yet"
Shift+Tab                        Normal Mode
/implement                       Approved plan only — single Claude
Esc Esc                          If Claude touches files outside the plan

Zero regressions                 Fix implementation not tests — 0 failures required
/test                            New behaviour + edge cases + regression test
/review                          Changed files only — not the whole codebase
Fix Critical and High            Run all tests after every fix
/simplify                        Quality pass
security-reviewer subagent       On changed files only
Final summary                    Files, tests, count, scope confirm
git commit                       You commit manually

/learn                           After every session without exception
Update CLAUDE.md                 Every correction compounds permanently

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ALWAYS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
50% context    /compact "preserve modified files and test status"
70% context    /compact immediately
90% context    /clear then restart session
Esc Esc        Undo when Claude goes off track
/model opus    Plan review and architecture decisions
/model sonnet  All implementation work
ultrathink     Add to any prompt for deep reasoning
/learn         After every session without exception
CLAUDE.md      Update after every correction — compounds forever
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Key Principles

**1. Plan before every action**
Never let Claude touch code without an approved plan.
The guard phrase — "address all notes, don't implement yet" — is non-negotiable.

**2. Foundation before features**
Phase 1 and Phase 2 must be solid and verified before any parallel work begins.
A shaky foundation makes parallel work slower, not faster.

**3. One file at a time, one module at a time**
Claude should only touch files in the approved plan.
If it touches anything else: `Esc Esc` and correct it immediately.

**4. Zero regressions, always**
Existing tests are the safety net.
Never skip, delete, or mark tests as pending to make the count green.
If a test breaks — fix the implementation.

**5. Learn after every session**
Run `/learn` after every session without exception.
Update `CLAUDE.md` after every correction.
This is compounding engineering — every mistake caught makes every future session better.

**6. You commit — Claude never commits**
Claude proposes, you approve, you commit.
This keeps you in control of what goes into your codebase.

---

*Sources: obra/superpowers · arjangirijobs/boris-cherny-claude-code · affaan-m/everything-claude-code · shanraisshan/claude-code-best-practice · Boris Cherny (creator of Claude Code) workflows*
