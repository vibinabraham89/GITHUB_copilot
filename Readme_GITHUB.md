The Complete GitHub Copilot Senior Developer Workflow

The 3 modes — memorise this first
Ask mode    → Question and answer. Copilot does NOT touch files.
              Use for: understanding code, asking questions, debugging

Plan mode   → Copilot thinks and proposes. You approve before anything happens.
              Use for: requirements, architecture, design, code review reports

Agent mode  → Copilot executes autonomously. Creates, edits, runs commands.
              Use for: repo setup, implementation, testing, observability, CI

How to switch modes: In Copilot Chat (Ctrl+Shift+I), use the dropdown at the top of the chat panel.
The core rule:
Unsure what to build?  → Plan mode
Know what to build?    → Agent mode
Want to understand?    → Ask mode

Step 0 — Fill in your project brief before touching Copilot

Create this file manually: docs/project-brief.md

Fill every section. The more honest and detailed this is, the better every downstream phase will be.
markdown# Project brief

## What is it?
One paragraph. What does this app do, for whom, and why does it exist?

## Who are the users?
- User type 1: <name> — <what they want to achieve>
- User type 2: <name> — <what they want to achieve>
- User type 3: <admin> — <what they manage>

## Core actions (must-haves)
Write as: "A <user> can <action>"
- A client can ...
- A freelancer can ...

## What it must NOT do (out of scope)
- No mobile app in v1
- No AI features in v1
- ...

## Hard constraints
- Must comply with GDPR
- Must support languages: ...
- No paid third-party services except Stripe
- ...

## Scale expectations
- Users at launch: ~500
- Users at 1 year: ~10,000
- Peak load: <describe scenario>
- Data volume: <e.g. 50 records per day>

## Performance expectations
- Page load: under <X> seconds
- API response: under <X> ms
- Availability: 99.9% uptime

## Tech preferences (leave blank if no preference)
- Language:
- Framework:
- Database:
- Hosting:
- Auth provider:

## Integrations needed
- Stripe — payments
- SendGrid — emails
- ...

## Success metrics
- <metric 1>: e.g. 50 projects posted in first month
- <metric 2>: e.g. payment dispute rate under 2%

## Open questions (be honest)
- Should users set hourly or fixed rates?
- How do we handle disputes?
- Do we need a mobile app in phase 2?
```

---

## Step 1 — Set up the repo

**Mode: Agent**

Open Copilot Chat, switch to Agent mode, then type:
```
Create the complete production-grade repo structure.

Create these folders and starter files:

.github/
  copilot-instructions.md
  prompts/
    01_requirements.prompt.md
    02_architecture.prompt.md
    03_design.prompt.md
    04_implementation.prompt.md
    05_testing.prompt.md
    06_observability.prompt.md
    07_review.prompt.md
  workflows/
    ci.yml

docs/
  project-brief.md        ← already exists, do not overwrite
  requirements.md         ← create empty
  architecture.md         ← create empty
  design.md               ← create empty
  decisions/
    adr-template.md       ← create with ADR format below
  api-contracts/
    openapi.yaml          ← create empty OpenAPI 3.0 skeleton

src/
  modules/
  shared/
    types/
      index.ts            ← empty export file
    errors/
      base.error.ts       ← BaseError class with status code and message
    logging/
      logger.ts           ← structured logger, outputs JSON with timestamp
  config/
    index.ts              ← loads and validates all env variables

tests/
  unit/
  integration/
  e2e/
  fixtures/

observability/
  dashboards/
  alerts/

.vscode/
  settings.json           ← format on save, eslint on save
  extensions.json         ← ESLint, Prettier, GitLens, Copilot

.env.example              ← list every required env variable with description
.gitignore                ← node_modules, .env, dist, coverage


For adr-template.md, use this format:
markdown# ADR-000: <title>

**Status:** Proposed | Accepted | Deprecated
**Date:** YYYY-MM-DD

## Context
What situation forced this decision?

## Options considered
1. Option A — pros and cons
2. Option B — pros and cons
3. Option C — pros and cons

## Decision
What did we choose and why?

## Consequences
What tradeoffs are we accepting?
What becomes easier? What becomes harder?
```

---

## Step 2 — Generate requirements

**Mode: Plan**

Switch to Plan mode. Type:
```
Read #docs/project-brief.md

Act as a product manager and staff engineer.

Propose a complete requirements document. Show me:
- The functional requirements you plan to write
  (format: "The system SHALL..." — numbered and testable)
- The non-functional requirements you identified
  (latency, availability, throughput targets)
- The constraints you found
- What you are marking as explicitly out of scope
- The edge cases and failure modes you spotted
- The success metrics you recommend
- The open questions that need my decision before proceeding
- The top 3 architectural decisions this idea will force

Do NOT write requirements.md yet.
Show me the full plan and wait for my approval.
```

Read the plan carefully. Push back on anything wrong. Add anything missing. Then say:

```
Approved. Now write the complete requirements.md into docs/requirements.md
Also create docs/decisions/adr-001-initial-decisions.md
with stubs for the top decisions you identified.
```

---

## Step 3 — Design the architecture

**Mode: Plan**
```
Read #docs/requirements.md

Act as a staff engineer designing for production.

Propose the architecture. Show me:
- The main components and what each one does
- The database design approach and why
- The API style you recommend (REST/GraphQL/gRPC) and the reason
- The caching strategy
- How you plan to handle async and background jobs
- The auth and authorisation model
- The top 3 failure modes and how the system recovers
- When does this architecture break — what is the scaling ceiling?

Then propose one alternative architecture and show
a tradeoff comparison between the two.

Do NOT write architecture.md yet.
Wait for my approval.
```

Review, challenge, approve. Then:

```
Approved. Write docs/architecture.md with the chosen approach.
Record the architecture decision in docs/decisions/adr-002-architecture.md
```

---

## Step 4 — Break into modules

**Mode: Plan**
```
Read #docs/requirements.md and #docs/architecture.md

Propose the full module breakdown. For each module show me:
- Name and single responsibility (one sentence only)
- The typed public interface — what functions it exposes
- What it depends on (other modules, DB, external APIs)
- What errors it owns vs what it propagates
- Any design risk you see in this module

Then show me:
- The complete recommended build order
  (dependency order — modules with no dependencies first)
- Any circular dependency risks

Do NOT create any files yet.
I want to review and approve the module list first.
```

Review carefully. This is the most important approval in the whole workflow. A wrong module boundary here means hours of rework later. Once approved:

```
Approved. Now:
1. Write the full module breakdown into docs/design.md
2. Write all shared types into src/shared/types/index.ts
3. Write the BaseError class into src/shared/errors/base.error.ts
4. Write the structured logger into src/shared/logging/logger.ts
5. Write the config loader into src/config/index.ts

Do not write any module service logic yet.
Foundation files only.
```

---

## Step 5 — Implement modules one at a time

**Mode: Agent**

Switch to Agent mode. Run this prompt once per module, starting from the one with no dependencies.
```
Read #docs/design.md and #src/shared/types/index.ts

Implement module: <ModuleName>

Build in this exact order:
1. Types specific to this module
   — import shared types from src/shared/types/index.ts
   — do not duplicate anything already in shared types

2. Custom error classes for this module
   — extend BaseError from src/shared/errors/base.error.ts
   — one error class per failure scenario

3. Service logic
   — pure functions where possible
   — no direct database calls inside service
   — all dependencies injected via constructor
   — max 30 lines per function, split if exceeded

4. Repository / infrastructure adapter
   — implements the interface defined in step 1
   — all DB calls go here only

5. Structured logging
   — every function: log on entry, on success, on every error
   — log format: { level, timestamp, module, operation, durationMs, error? }
   — use logger from src/shared/logging/logger.ts

6. Input validation
   — validate at every public boundary
   — throw named validation errors, never generic Error

Rules:
- Service logic never imports from infrastructure layer
- Every public function has JSDoc with @param and @returns
- Run TypeScript compiler after each file, fix errors before next file
- Never hardcode config values, always use src/config/index.ts

When this module is complete:
- Tell me every file you created
- Tell me which module should be built next based on the dependency order
- Stop and wait for me to say "continue" before starting the next module
```

Repeat for each module in dependency order. Review each one before typing "continue."

---

## Step 6 — Generate tests

**Mode: Agent**

Run once per module immediately after implementing it:
```
Read all files in #src/modules/<ModuleName>/
and #docs/design.md

Generate the complete test suite for <ModuleName>.

Round 1 — standard coverage:
- Unit test every exported function
- Test every branch — happy path and failure paths
- Integration tests with a real test database
  (use test containers, not mocks for DB)
- Write shared fixtures to tests/fixtures/<module>.fixtures.ts
- Mock all external services (HTTP calls, email, etc)

Round 2 — adversarial:
- What inputs would cause this module to fail silently?
  Generate those tests.
- What happens if the database is slow or disconnects mid-operation?
- What race conditions exist with concurrent calls?
  Test with parallel execution.
- What happens at the boundaries — empty arrays, null values,
  max string length, zero, negative numbers?

Round 3 — gap analysis:
Review all tests you just generated.
What scenarios are not yet covered?
Generate those tests now.

Write all tests into:
- tests/unit/<modulename>/
- tests/integration/<modulename>/

After generating, run the tests and fix any that fail.
Report the final coverage percentage.
```

---

## Step 7 — Add observability

**Mode: Agent**

Run once per module after tests pass:
```
Read all files in #src/modules/<ModuleName>/

Add production observability by editing the existing files directly.
Do not create new files unless absolutely necessary.

Add these to every service file:

1. Structured logging at:
   - Function entry: log the operation name and sanitised input
   - Function exit: log success and duration in ms
   - Every error path: log error type, message, and context
   - Log format: { level, timestamp, traceId, module, operation, durationMs }

2. Metrics instrumentation:
   - Counter for every operation (increment on call)
   - Counter for every error (increment on failure, tag with error type)
   - Histogram for operation duration (record durationMs)
   - Counter for validation failures (tag with field name)

3. Distributed tracing:
   - Create a span for every database call
   - Create a span for every external HTTP call
   - Tag spans with operation name and outcome

4. Health check contribution:
   - Add a healthCheck() method to the repository
     that verifies DB connectivity

After editing, show me a list of every change made
with the file name and what was added.
```

---

## Step 8 — Code review

**Mode: Plan first, then Agent**

First, switch to Plan mode:
```
Read all files in #src/modules/<ModuleName>/
and the corresponding tests in #tests/unit/<modulename>/
and #tests/integration/<modulename>/

Review this module as a principal engineer preparing
for a production incident post-mortem.

For every issue found, report:
- File name and approximate line number
- Risk level: Critical / High / Medium / Low
- What is wrong
- Why it is a risk
- What the correct fix is

Check specifically for:
- Bugs and off-by-one errors
- Missing error handling paths
- N+1 query problems or missing indexes
- Race conditions or missing locks
- Missing input sanitisation (injection, path traversal)
- Hardcoded values that should be in config
- SOLID violations — name the principle broken
- Missing observability gaps
- Security issues (auth bypass, data exposure, secrets in logs)

Do NOT fix anything yet.
Give me the full report and wait for my decision.
```

Read the report. Decide what to fix. Then switch to Agent mode:
```
Fix all Critical and High issues from your review report.
Leave Medium and Low issues as comments in the code.
After each fix show me:
- What file was changed
- What was wrong
- What you did to fix it

Run the tests after all fixes and confirm they still pass.
```

---

## Step 9 — CI/CD pipeline

**Mode: Agent**
```
Create .github/workflows/ci.yml

The pipeline must run on every push and pull request.
Include these jobs in order:

Job 1: quality
- Checkout code
- Setup Node 20 with npm cache
- npm ci
- Run ESLint
- Run TypeScript compiler check (tsc --noEmit)
- Run unit tests with coverage
- Fail if coverage is below 80%

Job 2: integration
- Checkout code
- Setup Node 20
- npm ci
- Start a Postgres 16 service container with health check
- Run integration tests against the real database

Job 3: security
- Checkout code
- Run npm audit, fail on high severity
- Run CodeQL analysis for JavaScript/TypeScript

Job 4: build
- Runs only after quality and security jobs pass
- Checkout code
- npm ci and npm run build
- Upload the dist folder as a build artifact

Also create a separate workflow: .github/workflows/pr-check.yml
This runs only on pull requests and adds:
- A comment on the PR with the test coverage report
- A check that fails if the PR description is empty
```

---

## Step 10 — The iteration loop (run per feature forever)

For every new feature after initial setup:
```
1. Update docs/project-brief.md with the new feature description

2. Plan mode:
   "Read #docs/requirements.md and #docs/project-brief.md
    What requirements need to be added or changed for <feature>?
    Show me the proposed changes before writing anything."
   → approve → agent writes to requirements.md

3. Plan mode:
   "Does this feature require any architecture changes?
    If yes, propose the changes and an ADR entry."
   → approve → agent updates architecture.md and creates ADR

4. Plan mode:
   "Does this feature require a new module or changes to existing ones?
    Show me the proposed module changes."
   → approve → agent updates design.md

5. Agent mode:
   Implement the new or changed module (Step 5 prompt)

6. Agent mode:
   Generate tests (Step 6 prompt)

7. Agent mode:
   Add observability (Step 7 prompt)

8. Plan then Agent:
   Review and fix (Step 8 prompts)

9. Commit:
   git commit -m "feat(<module>): <what and why>"

10. Push → CI runs automatically

The global Copilot instructions file
Paste this into .github/copilot-instructions.md. This file is read by Copilot automatically in agent and plan mode and controls its behaviour across the entire project.
markdownYou are a staff-level engineer on this project.
Always read docs/requirements.md, docs/architecture.md,
and docs/design.md before generating anything.
Always check docs/decisions/ before making architectural choices.

# Output rules
- Never produce partial implementations
- Never hardcode config — use src/config/index.ts
- Never import infrastructure from service layer
- Every public function needs JSDoc with @param and @returns
- Max function length: 30 lines — split if exceeded
- Max file length: 200 lines — split if exceeded

# Before writing any code
- Check src/shared/types/index.ts — use existing types, never duplicate
- Check src/shared/errors/base.error.ts — extend it, never throw raw Error
- Check src/shared/logging/logger.ts — use it, never use console.log

# Architecture rules
- Clean architecture strictly enforced
- Domain/service layer never imports from infrastructure layer
- All external I/O (DB, HTTP, queues) only in infrastructure layer
- All dependencies injected via constructor — never instantiated inside functions

# Code quality
- SOLID principles enforced — name the principle if you violate it
- No duplication — check if logic already exists before writing it
- Name variables for what they ARE, not what they DO

# Every implementation must include
- Types and interfaces
- Named error classes
- Service logic (pure)
- Infrastructure adapter
- Structured logging (entry, exit, every error)
- Input validation at every public boundary

# Testing — always generate alongside implementation
- Unit tests: every exported function, every branch
- Integration tests: every DB operation and API endpoint
- Edge cases: null, empty, max values, concurrent calls
- Coverage gate: 80% minimum

# If you identify an architectural decision
- Propose an ADR entry in docs/decisions/
- Do not proceed until I approve the decision

# Review mode
- Be critical, not polite
- Identify every risk with a severity level
- Name SOLID violations explicitly
- Flag missing observability by type (logging/metrics/tracing)
```

---

## Quick reference card
```
┌─────────────────────────────────────────────────────────┐
│  PHASE              MODE     YOU DO          COPILOT     │
├─────────────────────────────────────────────────────────┤
│  0 Repo setup       Agent    Nothing         Creates all │
│  1 Requirements     Plan     Fill brief      Proposes    │
│                     Plan     Approve         Writes doc  │
│  2 Architecture     Plan     Review          Proposes    │
│                     Plan     Approve         Writes doc  │
│  3 Module design    Plan     Review          Proposes    │
│                     Agent    Approve         Creates     │
│                              foundation files            │
│  4 Implementation   Agent    Review output   Builds      │
│                              one module at a time        │
│  5 Testing          Agent    Check coverage  Generates   │
│  6 Observability    Agent    Review changes  Instruments │
│  7 Review           Plan     Read report     Audits      │
│                     Agent    Approve fixes   Fixes       │
│  8 CI/CD            Agent    Nothing         Creates     │
│  9 Per feature      Loop     Steps 1-8       Iterates    │
└─────────────────────────────────────────────────────────┘

Ask mode  → "explain this", "why does this fail", "what does X mean"
Plan mode → anything involving thinking, proposing, or deciding
Agent mode → anything involving creating, editing, or running
