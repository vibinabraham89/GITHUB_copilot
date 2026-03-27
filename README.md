OpenAI - CHatgpt . 

Repo Setup (MANDATORY — This is your Copilot brain)

**Create this structure:**

.github/
  copilot-instructions.md
  prompts/
    01_requirements.md
    02_architecture.md
    03_design.md
    04_implementation.md
    05_testing.md
    06_review.md
docs/
  requirements.md
  architecture.md
  decisions.md
src/
tests/


**2. Copilot Instructions**

.github/copilot-instructions.md

You are a staff-level engineer building production-grade systems.

# Output Rules
- Never give partial implementations
- Always include edge cases
- Always include error handling
- Always include logging
- Always include types/interfaces

# Architecture
- Prefer clean architecture
- Enforce separation of concerns
- Suggest folder structure before coding

# Code Quality
- Follow SOLID
- Avoid duplication
- Use clear naming

# Testing
- Always generate:
  - unit tests
  - integration tests
  - edge cases

# Review Mode
- Be critical, not polite
- Highlight risks and tradeoffs


**3. Phase 1 — REQUIREMENTS**

Open docs/requirements.md

Use this prompt:

You are a product manager.

Convert this idea into a production-grade requirements document.

Include:
- functional requirements
- non-functional requirements
- edge cases
- constraints
- success metrics

Idea:
<your app idea>


**Pro move:**

Then refine:

Identify missing requirements, hidden risks, and scalability concerns.


**Phase 2 — ARCHITECTURE**

docs/architecture.md

Prompt:

Design a scalable architecture for this system.

Context:
<requirements.md>

Include:
- high-level architecture
- services/modules
- database design
- API design
- scaling strategy
- failure handling
- security considerations

🔥 Advanced Prompt (critical)
Challenge this architecture and suggest a better alternative with tradeoffs.

**Phase 3 — DESIGN BREAKDOWN**

docs/design.md

Prompt:

Break this architecture into implementable modules.

For each module:
- responsibilities
- inputs/outputs
- interfaces
- dependencies

Then:

Generate folder structure and file breakdown.


**Phase 4 — IMPLEMENTATION**

Step-by-step module generation

Implement module: <module_name>

Context:
<design.md>

Deliver:
- interfaces
- service logic
- error handling
- logging
- validation
  
🔥 Elite trick: “progressive generation”

After each file:

Now generate the next dependent file.
🔥 Context engineering

Keep these open in editor:

requirements.md
architecture.md
current module

**Phase 5 — TESTING**

Create /tests

Prompt:

Generate complete test suite for <module>

Include:
- unit tests
- integration tests
- edge cases
- failure scenarios
- mocks/stubs
- 
🔥 Next-level:

Identify untested scenarios and generate additional tests.

**Break your system intentionally**

Generate adversarial test cases to break this module.


**Phase 6 — REVIEW**


Prompt:

Review this module as a staff engineer.

Check:
- bugs
- scalability
- performance
- security
- maintainability

Be brutally honest.

Then:

Refactor the code based on your review.


**Phase 7 — ITERATION LOOP (REAL POWER)**

Run this loop per feature:

Generate
Test
Break
Fix
Optimize

**10. Copilot “Agents” via Prompt Files**

Inside .github/prompts/

📄 01_requirements.md
Act as a product manager and generate detailed requirements.
📄 02_architecture.md
Act as a staff engineer and design scalable architecture.
📄 03_design.md
Break architecture into modules and interfaces.
📄 04_implementation.md
Implement modules with production-grade quality.
📄 05_testing.md
Generate comprehensive test coverage.
📄 06_review.md
Perform critical code review and improvements.

👉 Now you just trigger Copilot using these files as context

**Performance Optimization Mode**
Analyze this system for bottlenecks.

Suggest:
- DB optimizations
- caching strategy
- async processing

**🔐 14. Security Hardening (DON’T SKIP)**
Perform security audit.

Check:
- input validation
- auth issues
- injection risks
- secrets handling

The Exact Workflow You Should Follow

For every feature:

Update requirements
Update architecture
Generate module
Generate tests
Run tests
Review with Copilot
Refactor
Commit



****CLaude Version ****


Phase 0 — Repo structure (significantly upgraded)
 

Here's the correct layout:
.github/
  copilot-instructions.md       ← global behavior (updated below)
  prompts/
    01_requirements.prompt.md
    02_architecture.prompt.md
    03_design.prompt.md
    04_implementation.prompt.md
    05_testing.prompt.md
    06_observability.prompt.md  ← NEW
    07_review.prompt.md
  workflows/
    ci.yml                      ← upgraded below

docs/
  requirements.md
  architecture.md
  decisions/                    ← NEW — ADR folder
    adr-001-database-choice.md
    adr-template.md
  api-contracts/                ← NEW — OpenAPI/contract specs
    openapi.yaml

src/
  modules/
  shared/
    types/                      ← global types/interfaces
    errors/                     ← custom error classes
    logging/                    ← logger setup
  config/                       ← env/config loading

tests/
  unit/
  integration/
  e2e/                          ← NEW
  fixtures/                     ← NEW — shared test data

observability/                  ← NEW
  dashboards/
  alerts/

.vscode/                        ← NEW — team-consistent editor config
  settings.json
  extensions.json

.env.example                    ← NEW — document required env vars


.github/copilot-instructions.md

The original is too vague. This version leverages Copilot's agent mode and workspace awareness:

markdown

You are a staff-level engineer. You have full context of this repo's 
requirements.md, architecture.md, and decisions/ folder. Read them 
before generating anything.

# Behavior rules
- Never produce partial implementations — complete the full module
- Always check docs/decisions/ before making architectural choices
- If a decision isn't recorded, propose an ADR entry alongside the code
- Never hardcode config — always use environment variables via src/config/
- Every public function needs a JSDoc/docstring with @param and @returns

# Output format per file
1. Interfaces/types first
2. Implementation
3. Error handling (custom error classes from src/shared/errors/)
4. Structured logging (use the logger from src/shared/logging/)
5. Validation at every boundary

# Architecture constraints
- Clean architecture: domain logic never imports from infrastructure
- All external I/O (DB, HTTP, queues) happens in the infrastructure layer
- Use dependency injection — never instantiate dependencies inside functions

# Code quality
- SOLID strictly enforced
- Max function length: 30 lines — split if exceeded
- Max file length: 200 lines — split if exceeded
- Name variables for what they ARE, not what they DO (user not getUser)

# Testing requirements (always generate alongside implementation)
- Unit tests: every public function, all branches
- Integration tests: every API endpoint and DB operation
- Edge cases: empty inputs, nulls, max limits, concurrent calls
- Coverage gate: 80% minimum

# Review mode
- Identify every risk — do not soften feedback
- Call out SOLID violations by name
- Flag any missing observability (logs, metrics, traces)
- Suggest the ADR entry for any architectural decision made
```

---

## Phase 1 — Requirements + ADR init

**Prompt (`01_requirements.prompt.md`):**

```
You are a product manager and staff engineer hybrid.

Convert this idea into a production-grade requirements document.

Include:
- Functional requirements (numbered, testable — "The system SHALL...")
- Non-functional requirements (latency, availability, throughput targets)
- Constraints (tech stack, compliance, budget)
- Out of scope (explicit — prevents scope creep)
- Edge cases and failure modes
- Success metrics (measurable)
- Open questions that need decisions

Then: identify the top 3 architectural decisions this requires.
For each, create an ADR stub in docs/decisions/ using this format:
  Title, Status: Proposed, Context, Options considered, Decision, Consequences

Idea: <your idea>
```

**Idea Structure :** ( To be given in requirements.md)

markdown

# Project brief

## What is it?
One paragraph. What does this app do, for whom, and why does it exist?

Example:
A freelance marketplace where small businesses post projects, 
freelancers bid on them, payments are held in escrow, and both 
sides review each other after completion.

## Who are the users?
List each type of user and what they need.

- User type 1: <name> — <what they want to achieve>
- User type 2: <name> — <what they want to achieve>
- User type 3: <name> — <admin/internal — what they manage>

## Core actions (the must-haves)
What are the 5–8 things the app absolutely must do?
Write as: "A <user> can <action>"

- A client can post a project with budget and deadline
- A freelancer can browse and bid on projects
- ...

## What it must NOT do (out of scope)
Be explicit. This prevents scope creep.

- No mobile app (web only for now)
- No AI matching (manual search only)
- No video calls built in

## Hard constraints
Non-negotiable requirements around compliance, platform, or environment.

- Must comply with GDPR (EU users)
- Must work on mobile browsers (no native app)
- Must support English and Arabic
- Budget: no paid third-party services except Stripe

## Scale expectations
Help the architect understand the load.

- Expected users at launch: ~500
- Expected users at 1 year: ~10,000
- Peak load scenario: <describe — e.g. "flash sale, 1000 concurrent">
- Data volume: <e.g. "~50 projects posted per day">

## Performance expectations
- Page load: under <X> seconds
- API response: under <X> ms
- Availability: <e.g. 99.9% uptime>

## Tech preferences (if any)
Leave blank if you have no preference — let the architect decide.

- Language: 
- Framework: 
- Database: 
- Hosting: 
- Auth provider: 

## Integrations needed
External services this app must connect to.

- Stripe — payments
- SendGrid — emails
- Google Maps — location search
- ...

## Success metrics
How will you know this app is working?

- <metric 1>: e.g. "50 projects posted in first month"
- <metric 2>: e.g. "freelancer bid rate > 30%"
- <metric 3>: e.g. "payment dispute rate < 2%"

## Open questions
Things you haven't decided yet. Be honest — these become ADRs.

- Should freelancers set hourly or fixed rates or both?
- How do we handle disputes — manual review or automated?
- Do we need a mobile app in phase 2?


---

## Phase 2 — Architecture + challenge loop

**Prompt (`02_architecture.prompt.md`):**

```
Context: <paste requirements.md>

Design a scalable architecture. Include:
- Component diagram (describe in text)
- API design (REST/GraphQL/gRPC — justify the choice)
- Database schema and indexing strategy
- Caching strategy with cache invalidation approach
- Async/queue strategy for heavy operations
- Auth and authorization model
- Failure modes and fallback strategies
- Scalability ceiling — when does this design break?

Then: challenge this architecture. Propose one alternative approach.
Show the tradeoff table: complexity vs scalability vs cost vs maintainability.
Record the final decision as an ADR entry.
```

---

## Phase 3 — Module design + contract-first

This is the most critical phase the original workflow underemphasizes.

**Prompt (`03_design.prompt.md`):**
```
Context: <paste architecture.md>

Break this into implementable modules. For each module:
- Single responsibility statement (one sentence)
- Public interface (typed — TypeScript interfaces or Python protocols)
- Input/output contracts
- Error conditions it owns vs errors it propagates
- External dependencies (other modules, DB, APIs)

Then:
1. Generate the full folder structure with file names
2. Generate the dependency graph (which module imports which)
3. Flag any circular dependency risks
4. Generate src/shared/types/index.ts with all shared types first
   — nothing gets implemented until types are agreed
```

---

## Phase 4 — Progressive implementation

The key upgrade here is **interfaces before implementation** and **one file at a time** with explicit handoff.

**Prompt (`04_implementation.prompt.md`):**
```
Context: <design.md open in editor> + <current module name>

Implement module: <module_name>

Deliver in this exact order:
1. Types/interfaces (import from src/shared/types/)
2. Custom error classes (extend from src/shared/errors/BaseError)
3. Service logic — pure functions where possible
4. Infrastructure adapters (DB calls, HTTP calls) — injected as interfaces
5. Structured log statements at: function entry, exit, and every error path
6. Input validation at every public boundary

After each file, say: "Next file: <filename> — type 'continue' to proceed"
This prevents context drift across long implementations.
```

---

## Phase 5 — Testing (with adversarial layer)

**Prompt (`05_testing.prompt.md`):**
```
Generate complete test suite for: <module_name>

Round 1 — standard coverage:
- Unit tests: every exported function, every branch
- Integration tests: real DB/API calls with test containers
- Mocks for all external dependencies
- Fixtures in tests/fixtures/

Round 2 — adversarial:
- What inputs would break this module? Generate those tests.
- What happens at max load? Test with large datasets.
- What race conditions exist? Test concurrent calls.
- What if dependencies are slow/fail mid-transaction?

Round 3 — gap analysis:
Review the tests you just generated. 
What scenarios are NOT covered? Generate them.
```

---

## Phase 6 — Observability (NEW — this is what separates senior from junior)

This phase didn't exist in the original and is critical for production systems.

**Prompt (`06_observability.prompt.md`):**
```
For module: <module_name>

Add production observability:

1. Structured logging — every log line must have:
   { level, timestamp, traceId, moduleId, operation, durationMs, error? }

2. Metrics — identify and instrument:
   - Throughput counters (operations/sec)
   - Latency histograms (p50, p95, p99)
   - Error rate gauges
   - Business metrics (orders placed, users created, etc.)

3. Distributed tracing — add span creation at:
   - Every external call (DB, HTTP, queue)
   - Every significant business operation

4. Alerts — define thresholds for:
   - Error rate > 1% sustained over 5 min
   - p99 latency > SLA target
   - Any 5xx response

5. Health check endpoint — what does it check?

Generate the observability/dashboards/<module>.json skeleton.
```

---

## Phase 7 — Staff engineer review

**Prompt (`07_review.prompt.md`):**
```
Review this module as a principal engineer preparing for a production incident.

Check and call out by name:
- Any bug or off-by-one
- Missing error handling paths
- N+1 query problems or missing indexes
- Race conditions or missing locks
- Missing input sanitization (XSS, injection, path traversal)
- Hardcoded values that should be config
- Missing observability (logs, metrics, traces)
- SOLID violations — name the principle broken
- Missing or wrong ADR entry

Score: risk level (Critical / High / Medium / Low) per issue.
Then: generate the refactored version with all issues fixed.

Phase 8 — Upgraded CI/CD
The original CI is too minimal. A production-grade pipeline:
yamlname: CI

on: [push, pull_request]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20', cache: 'npm' }
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check        # tsc --noEmit
      - run: npm run test:unit -- --coverage
      - name: Coverage gate
        run: npx coverage-check --lines 80

  integration:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env: { POSTGRES_PASSWORD: test }
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run test:integration

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm audit --audit-level=high
      - uses: github/codeql-action/init@v3
        with: { languages: javascript }
      - uses: github/codeql-action/analyze@v3

  build:
    needs: [quality, security]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm run build
      - uses: actions/upload-artifact@v4
        with: { name: dist, path: dist/ }

The upgraded iteration loop (Phase 9)

Per feature, run this exact sequence:

Update requirements.md with the feature's acceptance criteria
Add or update ADR if an architectural decision is involved
Update docs/architecture.md if the design changes
Generate module with Phase 4 prompt — one file at a time
Generate tests with Phase 5 prompt (all three rounds)
Add observability with Phase 6 prompt
Run tests locally — CI should be green before review
Phase 7 review — fix every Critical and High issue
Commit with message format: feat(module): what and why (not just what)
