Generic Claude Code Workflow — Works for Any Language
The only things that change between Python, TypeScript, Go, Java, Rust, or anything else are the commands in CLAUDE.md. The workflow, folder structure, agents, and process are identical.

The language translation table
Fill this into every file that references commands:
Language     Build              Test                  Lint/Format
──────────────────────────────────────────────────────────────────
TypeScript   npm run build      npm test              npm run lint
Python       python -m build    pytest                ruff check .
Go           go build ./...     go test ./...         golangci-lint run
Java         mvn package        mvn test              mvn checkstyle:check
Rust         cargo build        cargo test            cargo clippy
Ruby         bundle exec rake   bundle exec rspec     rubocop
PHP          composer install   ./vendor/bin/phpunit  ./vendor/bin/phpcs
Swift        swift build        swift test            swiftlint
Kotlin       ./gradlew build    ./gradlew test        ./gradlew ktlintCheck
Every place you see npm run build or npm test in the workflow below — replace with your language's equivalent from this table. That is the only customisation needed.

One-time setup — do this once, reuse on every project
bash# Install Superpowers (brainstorming, planning, TDD, review, branch management)
/plugin install superpowers@claude-plugins-official

# Install Everything Claude Code (28 agents, 125 skills, 60 commands)
/plugin marketplace add affaan-m/everything-claude-code
/plugin install everything-claude-code@everything-claude-code
```

---

## Project folder structure — language agnostic
```
your-project/
  CLAUDE.md                      ← loaded every session, under 200 lines
  REVIEW.md                      ← code review rules separate from CLAUDE.md
  .claude/
    settings.json                ← permissions, hooks, model — check into git
    commands/
      plan.md                    → /plan
      implement.md               → /implement
      review.md                  → /review
      test.md                    → /test
      fix-issue.md               → /fix-issue
      checkpoint.md              → /checkpoint
      learn.md                   → /learn
    agents/
      planner.md
      code-reviewer.md
      security-reviewer.md
      test-writer.md
      verify-app.md
    skills/
      project-conventions/
        SKILL.md                 ← your patterns, idioms, naming rules
      db-patterns/
        SKILL.md
      api-conventions/
        SKILL.md
    rules/
      coding-style.md            ← language-specific style rules
      git-workflow.md
      testing.md
  docs/
    plan.md                      ← active task plan, annotate directly
    requirements.md
    architecture.md
    decisions/                   ← ADRs
  tasks/
    lessons.md                   ← self-improvement log
  .mcp.json                      ← MCP servers, check into git

CLAUDE.md — generic template, fill in your stack
markdown# [Project name]
[One sentence: what this project does and who uses it]

# Commands — any developer runs these on first try
Build:  [your build command]
Test:   [your test command]
Lint:   [your lint command]
Format: [your format command]
Run:    [your dev/run command]

# Stack
Language:  [Python / TypeScript / Go / Java / Rust / etc]
Framework: [Django / Next.js / Gin / Spring / Actix / etc]
Database:  [Postgres / MySQL / MongoDB / SQLite / etc]
Package manager: [pip / npm / go mod / maven / cargo / etc]

# Behaviour — always enforced
- Use Plan Mode (Shift+Tab) for any task touching 3 or more files
- Never push directly to the main branch
- Never skip or delete existing tests
- Never mark a task complete without running tests
- Only change files listed in the approved plan
- Stop and tell me if something unexpected appears
- Commit after every completed task

# Code — always enforced
- Match the existing code style in every file touched
- Never introduce patterns not already in this codebase
- All config via environment variables — nothing hardcoded
- Max 30 lines per function — split if longer
- Every public function needs a docstring or equivalent comment

# Error handling — always enforced
- Use named error types — never throw/raise generic exceptions
- Log on every error path with: level, timestamp, module, operation, error

# Testing — always enforced
- Run tests after every file change
- Zero regressions — fix implementation not tests
- New behaviour always needs a new test
- Minimum 80% coverage

# Context management — always enforced
- At 50% context: /compact — say "preserving: [modified files] + test status"
- At 90% context: /clear — fresh session, paste current task again

# After any correction
- Add to tasks/lessons.md: what went wrong + rule that prevents it
- Suggest CLAUDE.md additions if the rule should apply permanently

# @import .claude/rules/coding-style.md
# @import .claude/rules/git-workflow.md
# @import .claude/rules/testing.md
# @import tasks/lessons.md

settings.json — generic, language agnostic
Replace [BUILD], [TEST], [LINT], [FORMAT] with your actual commands:
json{
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
Python example:
json"command": "ruff format . || true"
"command": "pytest -q 2>&1 | tail -3"
Go example:
json"command": "gofmt -w . || true"
"command": "go test ./... 2>&1 | tail -3"

The commands — copy exactly, they work for any language
.claude/commands/plan.md
markdownRead docs/requirements.md and docs/architecture.md if they exist.
Read the existing codebase structure using grep and glob.

Write a complete phased implementation plan into docs/plan.md

For each task:
- Exact file paths to create or change
- What the change does and why
- The failing test to write first (TDD — red before green)
- How to verify it works

Tasks must be 2-5 minutes each.
Break into phases: Phase 1 foundation, Phase 2 core, Phase 3 integration.

Do not implement. Write the plan only and stop.
.claude/commands/implement.md
markdownRead docs/plan.md

Execute the plan exactly as written.

Rules:
- Change only files listed in the plan
- Match the existing code style in every file
- Run the build command after each file — fix errors before the next file
- Write the failing test before the implementation for every task (TDD)
- Stop and report if anything unexpected appears
- Never refactor anything outside the current task scope

After each task: say what you built and what is next.
Stop after each phase. Wait for me to say continue.
.claude/commands/review.md
markdownRead all files changed in the current task.

Review as a principal engineer preparing for a production incident.

For each issue:
- File name and line number
- Risk: Critical / High / Medium / Low
- What is wrong
- The exact fix

Check:
- Bugs and off-by-one errors
- Missing error handling
- Security: unvalidated input, exposed data, secrets in logs
- Violations of existing patterns in this codebase
- Files touched that were not in the plan
- Missing or inconsistent logging

Review changed files only. Do not comment on unrelated code.
Report issues only. Do not fix yet. Wait for my instruction.
.claude/commands/test.md
markdownRead all recently changed files.

Generate tests covering:
1. New behaviour — happy path and every failure path
2. Edge cases: null/nil/None, empty, max values, zero, concurrent calls
3. One regression test that permanently locks this feature in

Use the same test framework already in this project.
Follow the existing test file naming and folder structure exactly.
Run all tests — old and new together.
Report: X passing, 0 failing, coverage percentage.
.claude/commands/fix-issue.md
markdownIssue: $ARGUMENTS

Read the relevant source files first.
Find the root cause — do not guess.

Then:
1. Write a failing test that reproduces the bug exactly
2. Fix the implementation until that test passes
3. Confirm no existing tests broke
4. Add to tasks/lessons.md: what caused this + rule that prevents it
.claude/commands/checkpoint.md
markdownSave current state to tasks/current-task.md:
- Every file modified so far
- Every file created so far
- Test status: X passing, Y failing
- Current phase and what remains
- Any decisions made not captured in the plan
.claude/commands/learn.md
markdownReview this session.

Extract:
- Mistakes made and corrected
- Patterns that worked well
- What took longer than expected and why

Write to tasks/lessons.md:
- Mistake: [what happened]
- Rule: [what prevents it next time]

Then suggest additions to CLAUDE.md based on these findings.

The agents — generic, work for any language
.claude/agents/planner.md
markdown---
name: planner
description: Creates detailed implementation plans before any code is written
model: opus
effort: high
tools: Read, Grep, Glob, Write
---
You are a staff engineer creating an implementation plan.

Read the codebase using grep and glob to understand patterns.
Write a plan specific enough for a junior developer to follow.

Every task must have:
- Exact file paths
- Specific changes described concisely
- The failing test to write first
- Verification steps

Break tasks into 2-5 minute chunks.
Order tasks by dependency — prerequisites first.
Flag every risk and ambiguity explicitly.
Do not write any implementation code.
.claude/agents/code-reviewer.md
markdown---
name: code-reviewer
description: Reviews code as a principal engineer for bugs, security, and quality
model: opus
effort: high
tools: Read, Grep, Glob
---
You are a principal engineer reviewing for production readiness.

Read only the changed files. Never comment on unrelated code.

For every issue:
- File name and exact line number
- Risk: Critical / High / Medium / Low
- What is wrong and why it matters
- The exact fix

Check: bugs, missing error handling, security gaps,
violation of existing codebase patterns, missing tests,
inconsistent logging, anything outside the approved change scope.

Be critical. Politeness hides risk.
.claude/agents/security-reviewer.md
markdown---
name: security-reviewer
description: Reviews code for security vulnerabilities across any language
model: opus
effort: high
tools: Read, Grep, Glob
---
You are a senior security engineer.

Review for:
- Injection vulnerabilities (SQL, command, path traversal, template)
- Authentication and authorisation flaws
- Sensitive data in logs, responses, or error messages
- Unvalidated or unsanitised input at every public boundary
- Hardcoded secrets, API keys, passwords
- Insecure dependencies or known vulnerable patterns

Provide file name, line number, severity, and exact fix for each finding.
Severity: Critical / High / Medium / Low
.claude/agents/test-writer.md
markdown---
name: test-writer
description: Writes comprehensive tests for any module in any language
model: sonnet
tools: Read, Write, Bash
---
You are a senior QA engineer.

For any module given:
1. Read all files in the module
2. Write unit tests for every exported/public function — every branch
3. Write integration tests for every external call
4. Write adversarial tests — what inputs break this?
5. Write one permanent regression test for this feature
6. Run the test suite and fix any setup issues
7. Report final coverage percentage

Use the test framework already present in this project.
Follow existing test naming and folder conventions exactly.
.claude/agents/verify-app.md
markdown---
name: verify-app
description: Verifies the application is production ready after any change
model: sonnet
tools: Read, Bash
---
You are a QA engineer doing a pre-release check.

Run in this exact order:
1. Build — must pass with zero errors
2. Tests — must pass with zero failures
3. Lint — must pass with zero errors
4. Check no secrets or credentials in changed files
5. Check no debug statements left in production code
6. Check no TODO comments left in changed files
7. Verify all environment variables in .env.example are documented

Report pass or fail for each check with specific details on failures.
Final verdict: READY or NOT READY

Workflow A — New Product
Before opening Claude Code
bashmkdir my-project && cd my-project
git init

# Create the folder structure
mkdir -p .claude/commands .claude/agents .claude/skills .claude/rules
mkdir -p docs tasks src tests

# Create CLAUDE.md  ← paste the template above, fill in your stack
# Create .claude/settings.json  ← paste the template above, fill in your commands
# Create each .claude/commands/*.md file  ← paste from above
# Create each .claude/agents/*.md file  ← paste from above

git add . && git commit -m "chore: project setup with Claude Code config"

claude
```

## Step 1 — Brainstorm and spec

Press `Shift+Tab` — enter Plan Mode. Then:
```
I want to build: [your idea]

Ask me questions using the AskUserQuestion tool.
Interview me until you understand:
- Who the users are and what they need
- The core actions the system must support
- What is explicitly out of scope in version 1
- Any hard constraints (compliance, performance, tech stack)
- How we will measure success

Do not write any code or files. Interview me first.
```

Answer every question. After the interview:
```
Based on our conversation, write a complete spec to docs/requirements.md

Include:
- Functional requirements (numbered, testable — "The system SHALL...")
- Non-functional requirements (latency, availability targets)
- Out of scope explicitly listed
- Success metrics
- Open decisions needing an ADR

Then propose the high-level architecture with module breakdown.
Show build order based on dependencies.
Do not implement. Spec and architecture only.
```

Read `docs/requirements.md`. Fix anything wrong. Add anything missing.

## Step 2 — Write the phased plan

Still in Plan Mode:
```
/plan
Press Ctrl+G to open docs/plan.md in your editor. Annotate directly:
markdown## Task 4: Create user repository
> NOTE: use ORM not raw SQL
> NOTE: soft delete — never hard delete user data

## Task 7: Login endpoint
> NOTE: POST not GET
> NOTE: rate limit this endpoint
> NOTE: never log the password in any form
```

Send back with the guard phrase — never skip this:
```
Address all notes in docs/plan.md
Do not implement yet. Show me the updated plan.
Repeat annotating and sending back until zero ambiguity remains in the plan.
Step 3 — Challenge the plan with a second Claude session
Open a second terminal tab in the same project:
bashclaude
```

In this second session:
```
Read docs/plan.md and docs/requirements.md

Review this plan as a principal engineer.

Find:
- Tasks in wrong order (dependency violations)
- Missing tasks that will cause failures later
- Tasks too vague to implement correctly
- Security gaps
- Missing tests
- Anything overengineered

Severity: Critical / High / Medium
Be brutal — this plan controls everything.
Update docs/plan.md in the main session based on the feedback.
Step 4 — Isolated worktree and implement
Back in the main session. Switch to Normal Mode (Shift+Tab):
bashclaude --worktree
```

Then:
```
/implement
```

Claude works through the plan phase by phase. For each task it writes the failing test first, then the implementation, then verifies it passes. Superpowers enforces TDD automatically.

**If Claude touches files outside the plan:**
```
Esc Esc
```

Then:
```
You changed [file] which is not in the plan.
Revert that change. Only work on files in docs/plan.md
```

**If something unexpected is discovered mid-implementation:**
```
Stop. Update docs/plan.md with what we discovered.
Then continue from where we stopped.
```

## Step 5 — Checkpoint after every phase

After each phase:
```
/checkpoint
```

Then:
```
Use the verify-app subagent.
Confirm build passes, tests pass, lint passes.
Report READY or NOT READY.
```

Never start the next phase until the current one is READY.

## Step 6 — Quality and security

After all phases complete:
```
/simplify
```

Then:
```
Use the security-reviewer subagent on src/
```

## Step 7 — Review and merge
```
/review
```

Fix all Critical and High issues. Run tests after every fix.
```
/finish-development-branch
```

Superpowers presents options: merge, open PR, keep branch, or discard.

## Step 8 — Learn
```
/learn
```

Then:
```
Based on tasks/lessons.md, what rules should be added
to CLAUDE.md to prevent these issues permanently?
Show me the exact lines to add.
Update CLAUDE.md. Every correction improves every future session.

Workflow B — Single Feature in Existing Project
Before opening Claude Code
bash# Always branch first — never work on main
git checkout -b feature/<name>

# Verify clean baseline
git status       # nothing uncommitted
[TEST COMMAND]   # must be fully green

# If tests fail before you touch anything: fix the baseline first
# Never start a feature change on a broken codebase
Step 1 — Map the existing codebase
bashclaude
```
```
Read the src/ folder using grep and glob.

Map what exists:
- Every module and its single responsibility
- Naming conventions used consistently
- Error handling pattern in use
- Logging format and which logger
- How database calls are structured
- How external services are called
- Test structure and current coverage

Do not suggest any changes. Map only.
```

Read the map. Correct anything Claude got wrong about your project.

## Step 2 — Impact analysis
```
I want to add this feature:
[describe the feature in plain language]

Based on what you just read, answer without touching any files:

1. Which files need to change and exactly why?
2. Which files are affected indirectly?
3. Which existing tests cover the code being changed?
4. Does this need a database migration? If yes, what changes?
5. Does this change any API request or response contracts?
6. What is the riskiest part of this change?
7. Is there any tech debt that makes this harder?

Analysis only. No solutions.
```

Read carefully. Adjust scope if the impact is larger than expected.

## Step 3 — Plan and approve

Press `Shift+Tab` — enter Plan Mode:
```
/plan
```

Press `Ctrl+G` — open `docs/plan.md` — annotate every decision you want to influence.

Send back with the guard phrase:
```
Address all notes in docs/plan.md
Do not implement yet. Show me the updated plan.
```

Approve only when zero ambiguity remains.

## Step 4 — Implement

Press `Shift+Tab` — back to Normal Mode:
```
/implement
```

The one non-negotiable rule: if Claude touches any file not in the approved plan, stop it immediately:
```
Esc Esc
Stop. [filename] is not in the plan. Revert that change.
Only change files in docs/plan.md
```

## Step 5 — Zero regressions
```
Run all existing tests for the changed modules.

For every failure:
- Show the exact error message
- Is this a real regression from our change?
  Or does the test need updating because behaviour intentionally changed?

If real regression: fix the implementation — not the test.
If legitimate change: update the test with a comment explaining why.

Never skip, mark pending, or delete any test.
Zero failures required before proceeding.
```

If you cannot reach zero failures: go back to Step 4. Do not proceed on broken ground.

## Step 6 — New tests
```
/test
```

## Step 7 — Review, fix, quality, security
```
/review
```

Fix Critical and High issues:
```
Fix all Critical and High issues from the review.
Do not touch files outside those already changed.
Run all tests after every fix.
```

Quality pass:
```
/simplify
```

Security on changed files only:
```
Use the security-reviewer subagent on these files:
[list the changed files]
```

## Step 8 — Final check and commit
```
Give me the final summary:
1. Every file changed
2. Every new test added
3. All tests passing — confirm with count
4. No tests deleted or skipped — confirm
5. Scope stayed within the approved plan — confirm
6. Anything to manually verify in the running application?
You commit manually — Claude never commits for you:
bashgit add .
git commit -m "[type]([module]): [what changed and why]

- [specific change 1]
- [specific change 2]
- migration: [schema change if applicable]

Closes #[issue number]"

git push origin feature/<name>
```

Commit types: `feat`, `fix`, `refactor`, `test`, `chore`, `docs`

## Step 9 — Learn
```
/learn
```

Update CLAUDE.md with any new rules.

---

## Context management — the numbers that never change
```
0–50%    Work freely
50%      /compact — "preserve: modified files list, test status, current phase"
70%      /compact immediately
90%+     /clear — start fresh session, paste docs/plan.md again

Esc Esc  Undo last agent action
/rewind  Roll back to previous checkpoint
```

## Model — right model for right task
```
Default: sonnet (set in settings.json — handles 80% of tasks)

Switch to opus:
  /model opus    → plan review, architecture decisions, complex debugging
  ultrathink     → add to any prompt for deep extended reasoning

Back to sonnet:
  /model sonnet  → as soon as planning is complete
```

---

## The complete quick reference card
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NEW PRODUCT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Shift+Tab              Plan Mode
Brainstorm             AskUserQuestion interview
/plan                  Phased plan to docs/plan.md
Ctrl+G + annotate      Edit plan.md directly
Guard phrase           "address all notes, don't implement yet"
Second Claude tab      "review plan as principal engineer"
Shift+Tab              Normal Mode
claude --worktree      Isolated branch
/implement             TDD: test first, then code
/checkpoint            After every phase
verify-app agent       Build + test + lint check
/simplify              Quality pass
security-reviewer      Full codebase security audit
/review                Staff engineer review
Fix Critical+High      Then run tests
/finish-development-branch  Merge or PR
/learn                 Extract session patterns
Update CLAUDE.md       Permanent improvement

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FEATURE CHANGE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
git checkout -b        Branch first — always
[TEST COMMAND]         Clean baseline — must be green
Map codebase           "read src/ using grep+glob, map only"
Impact analysis        Files, tests, migration, risks
Shift+Tab              Plan Mode
/plan                  Feature plan to docs/plan.md
Ctrl+G + annotate      Edit plan.md directly
Guard phrase           "address all notes, don't implement yet"
Shift+Tab              Normal Mode
/implement             Approved plan only
Esc Esc                If Claude goes off-scope
Zero regressions       Fix implementation not tests
/test                  New behaviour + regression test
/review                Changed files only
Fix Critical+High      Then run tests
/simplify              Quality pass
security-reviewer      Changed files only
Final summary          Files, tests, count, scope confirm
git commit             You commit manually — never Claude
/learn                 Extract patterns
Update CLAUDE.md       Permanent improvement

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ALWAYS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
50% context  /compact "preserve modified files + test status"
90% context  /clear then restart
/model opus  for planning and architecture
/model sonnet for implementation
ultrathink   in any prompt for deep reasoning
Esc Esc      undo when Claude goes off track
/learn       after every session without exception
CLAUDE.md    update after every correction — compounds forever
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
