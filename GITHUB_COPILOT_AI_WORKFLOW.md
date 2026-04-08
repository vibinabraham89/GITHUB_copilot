# The Complete GitHub Copilot Workflow
### For building AI apps — Python backend, optional React/TypeScript frontend
### Ship fast as MVP. Harden to production. Never rewrite.

---

## ⚠️ Read This Before Anything Else

This workflow has two speeds. Pick one before you open Copilot.

```
MVP     = 40% of this workflow   → ship in days, validate the idea
PROD    = 100% of this workflow  → only after you have real users
```

Every step is tagged:
- `[MVP]`  — do this in MVP mode
- `[PROD]` — production only, skip in MVP
- `[BOTH]` — mandatory in both modes, no exceptions

The biggest mistake solo developers make is running the full workflow
on an unvalidated idea. You burn a week before the first user sees it.
Ship first. Harden after.

---

## Table of Contents

- [The 3 Modes](#the-3-modes)
- [Two-Track Decision Gate](#two-track-decision-gate)
- [The Dual Model Review Protocol](#the-dual-model-review-protocol)
- [Step 0 — Project Brief](#step-0--project-brief-both)
- [Step 0.1 — Scope and Mode](#step-01--scope-and-mode-both)
- [Step 1 — Repo Setup](#step-1--repo-setup-both)
- [Step 2 — Requirements](#step-2--requirements-both)
- [Step 3 — Architecture](#step-3--architecture-both)
- [Step 3.5 — Data Layer Strategy](#step-35--data-layer-strategy-both)
- [Step 3.6 — AI System Design](#step-36--ai-system-design-both)
- [Step 3.7 — Frontend Design](#step-37--frontend-design-fullstack-only)
- [Step 4 — Module Design](#step-4--module-design-both)
- [Step 5 — Implementation](#step-5--implementation-both)
- [Step 6 — Testing](#step-6--testing-both)
- [Step 7 — Observability](#step-7--observability-prod)
- [Step 8 — AI Evaluation Pipeline](#step-8--ai-evaluation-pipeline-prod)
- [Step 9 — User Feedback Loop](#step-9--user-feedback-loop-both)
- [Step 10 — Deployment Strategy](#step-10--deployment-strategy-both)
- [Step 11 — Final Code Review](#step-11--final-code-review-both)
- [Step 12 — CI/CD Pipeline](#step-12--cicd-pipeline-both)
- [Step 13 — Iteration Loops](#step-13--iteration-loops-both)
- [AI App Rules](#ai-app-rules)
- [Copilot Instructions File](#copilot-instructions-file)
- [Quick Reference Card](#quick-reference-card)
- [Part 2 — Feature Change in Existing Project](#part-2--feature-change-in-existing-project)
  - [Change Size Classification](#change-size-classification)
  - [Before Opening Copilot](#before-opening-copilot)
  - [FC Step 0 — Baseline Check and No-Test Scenarios](#fc-step-0--baseline-check-and-no-test-scenarios)
  - [FC Step 1 — Map the Codebase](#fc-step-1--map-the-codebase)
  - [FC Step 2 — Impact Analysis](#fc-step-2--impact-analysis)
  - [FC Step 3 — Propose and Approve the Plan](#fc-step-3--propose-and-approve-the-plan)
  - [FC Step 3.5 — Backward Compatibility Check](#fc-step-35--backward-compatibility-check)
  - [FC Step 3.6 — Feature Flag Strategy](#fc-step-36--feature-flag-strategy)
  - [FC Step 3.7 — AI Prompt Change Risk Gate](#fc-step-37--ai-prompt-change-risk-gate)
  - [FC Step 4 — Implement the Change](#fc-step-4--implement-the-change)
  - [FC Step 4.5 — Performance Impact Check](#fc-step-45--performance-impact-check)
  - [FC Step 5 — Verify Existing Tests Pass](#fc-step-5--verify-existing-tests-pass)
  - [FC Step 6 — Write New Tests](#fc-step-6--write-new-tests)
  - [FC Step 7 — Review the Diff](#fc-step-7--review-the-diff)
  - [FC Step 8 — Pre-Commit Safety Check and Commit](#fc-step-8--pre-commit-safety-check-and-commit)
  - [FC Step 8.5 — Staging Verification](#fc-step-85--staging-verification)
  - [FC If Agent Goes Off-Scope](#fc-if-agent-goes-off-scope)
  - [FC Quick Reference](#fc-quick-reference)

---

## The 3 Modes

```
Ask mode    → Copilot reads and explains. Touches ZERO files.
              Use for: understanding, debugging, explaining errors

Plan mode   → Copilot proposes. You approve before ANYTHING is written.
              Use for: requirements, architecture, design, review reports

Agent mode  → Copilot executes. Creates and edits files.
              Use for: repo setup, implementation, testing, CI/CD
```

```
Unsure what to build?    → Plan mode
Know what to build?      → Agent mode
Want to understand?      → Ask mode
Something broke?         → Ask mode first, then fix
```

Switch modes: Ctrl+Shift+I → dropdown at top of Copilot Chat panel

---

## Two-Track Decision Gate

**Decide before you open Copilot. This changes everything downstream.**

### MVP Track [MVP] — ship in days

```
GOAL: working software in real users hands, fast

SKIP entirely:
  ✗  Observability (Step 7)
  ✗  Automated AI evaluation pipeline (Step 8)
  ✗  Full CI/CD pipeline (Step 12)
  ✗  ADR documents
  ✗  Performance and load testing
  ✗  Prompt regression test suite

DO FAST (lightweight version):
  ⚡  Requirements    → one page, bullet points, 30 minutes max
  ⚡  Architecture    → rough text description, 20 minutes max
  ⚡  Data layer      → SQLite only, no cache, no queue
  ⚡  Deployment      → Railway or Render, single-command deploy
  ⚡  Dual review     → AI files and auth files ONLY
  ⚡  Review gate     → Critical issues only, skip High/Medium/Low
  ⚡  Testing         → happy path + the failure that kills your demo
  ⚡  User feedback   → thumbs up/down only, stored in SQLite

MANDATORY even in MVP — these are never skipped:
  ✅  .env.example with every required key documented
  ✅  Input validation on all public endpoints
  ✅  AI API error handling with retry logic
  ✅  Secrets in environment variables, never in code
  ✅  User feedback collection (thumbs up/down minimum)
  ✅  Working deployment — app must reach real users
```

### Production Track [PROD] — when users depend on you

```
GOAL: reliable, observable, scalable, secure

ALL steps — no exceptions
Full dual-model review on every single file
Observability on every module
Automated AI evaluation with scoring
Full CI/CD with all quality gates
Minimum 80% test coverage
Performance and load testing
Security audit before every deploy
User feedback loop feeding AI improvement
```

### Graduation rule

```
Start on MVP track.
Move to Production track when ANY of these become true:
  ▸ 50+ active users
  ▸ You are charging money
  ▸ You are handling sensitive user data
  ▸ A bug would cause real harm

Do not graduate early. Shipping beats perfecting.
```

---

## The Dual Model Review Protocol

**Copilot builds. A second AI (Claude or ChatGPT) reviews.**
Copilot cannot reliably catch its own mistakes. A cold reviewer does.

```
MVP track:     Review AI layer files and auth files only
               Stopping condition: zero CRITICAL issues
Production:    Review every single file
               Stopping condition: zero CRITICAL + zero HIGH issues
```

### The reviewer prompt — copy this once, reuse forever

Send this to Claude or ChatGPT. Paste your file at the bottom.

```
You are a senior Python engineer doing a strict code review
for an AI application backend.

Score every issue using this exact format:
[CRITICAL] — breaks in production, must fix before continuing
[HIGH]      — significant risk, fix before next file
[MEDIUM]    — fix in this session, does not block
[LOW]       — note for later, does not block

Check for:

CORRECTNESS
- Logic errors and off-by-one errors
- Async/await misuse (missing await, sync call in async context)
- Missing return values or wrong return types

ERROR HANDLING
- Unhandled exceptions, especially from AI API calls
- Missing timeout on external calls
- No retry logic on transient failures
- Silent failures that swallow exceptions

SECURITY
- Unvalidated input entering the system
- Prompt injection vulnerability
- API keys or secrets hardcoded
- Missing authentication on protected endpoints

PYTHON SPECIFIC
- Missing type annotations on public functions
- Pydantic not used where it should be
- Mutable default arguments (def f(x=[]) is a bug)
- Missing docstrings on public functions and classes

AI SPECIFIC (only if this file touches AI logic)
- User input concatenated directly into prompt string
- No token limit check before model call
- Model response not validated before use
- No fallback when model call fails
- No cost tracking per call
- No timeout on model call

SOLID VIOLATIONS
- Single Responsibility: doing more than one thing
- Dependency Inversion: depending on concrete implementations

PERFORMANCE
- N+1 database queries
- Sync I/O blocking an async event loop

Output format — one line per issue:
[SEVERITY] filename:line — description — suggested fix

End with exactly one of:
VERDICT: PASS    (zero Critical, zero High)
VERDICT: FAIL    (any Critical or High remaining)

File to review:
[PASTE FILE CONTENT HERE]
```

### The fix prompt — send to Copilot after review

```
The external reviewer found these issues in [filename]:

[PASTE FULL REVIEW OUTPUT HERE]

MVP:  Fix all CRITICAL issues only.
PROD: Fix all CRITICAL and HIGH issues.

Rules:
- Fix only what is listed in the review
- Do not change anything not flagged
- Run lint and type check after every fix
- Show me a diff of every change made
- Confirm the build still passes
```

---

## Step 0 — Project Brief [BOTH]

**Mode: none — you write this manually. 15-30 minutes.**

Create docs/project-brief.md. Fill every section.

```markdown
# Project brief

## What is it?
One specific paragraph.
Bad:  "An AI chat app"
Good: "An AI code review tool that analyses Python files and returns
      line-level improvement suggestions using GPT-4o, for solo
      developers who want faster feedback without a human reviewer."

## Who are the users?
- User type 1: [role] — [what they want to achieve]
- User type 2: [role] — [what they want to achieve]

## Core actions
Format: "A [user] can [action with specific outcome]"
- A developer can upload a .py file and receive suggestions within 10s
- A developer can accept or reject each suggestion individually

## Out of scope for this version
Explicit list. Prevents scope creep.
- No mobile app
- No real-time collaboration

## AI requirements
- Model: [GPT-4o / Claude 3.5 Sonnet / open source — pick one]
- Pattern: [simple completion / RAG / agent with tools / multi-agent]
- Streaming: [yes — user sees tokens arrive / no — wait for full response]
- Tokens per request estimate: [e.g. 2000 input + 500 output]
- Requests per day estimate: [e.g. 200 at launch]

## Hard constraints
- Compliance: [GDPR / HIPAA / none]
- Response time target: [e.g. first token within 2 seconds]
- Cost ceiling per request: [e.g. $0.05 max]
- Data residency: [e.g. EU only / no restriction]

## Tech preferences
- Backend: Python + [FastAPI / Django / Flask]
- Database: SQLite for MVP, upgrade decision in Step 3.5
- Vector DB: [needed / not needed]
- AI SDK: [raw SDK / LangChain / LlamaIndex / CrewAI]
- Frontend: [React + TypeScript / none — backend API only]

## Integrations
- AI API: [OpenAI / Anthropic / etc]
- Auth: [Supabase Auth / Auth0 / custom JWT]
- Storage: [S3 / local filesystem]

## Success metrics (measurable numbers only)
- [metric]: e.g. "95th percentile response under 3 seconds"
- [metric]: e.g. "AI thumbs-up rate above 70%"

## Open questions
- [unresolved decision]
```

---

## Step 0.1 — Scope and Mode [BOTH]

**Mode: Plan**

Answer these two questions yourself first, then run the Copilot prompt.

```
SCOPE: fullstack          (Python backend + React/TypeScript frontend)
       OR
SCOPE: backend-only       (Python API only, no frontend in this workflow)

MODE: mvp
      OR
MODE: production
```

Copilot prompt:

```
Read #docs/project-brief.md

My decisions:
SCOPE: [your answer]
MODE: [your answer]

Answer these 7 questions from the brief.
Do not propose architecture. Do not write any files. Answers only.

1. What AI pattern does this require?
   (simple completion / RAG / agent with tools / multi-agent)
   Justify from the brief.

2. Which AI framework do you recommend and why?
   (raw SDK / LangChain / LlamaIndex / CrewAI)
   Justify based on the pattern above.

3. Is a vector database needed?
   If yes: which one and why for this specific use case.
   If no: what handles retrieval instead.

4. Is streaming required for this UX?
   Justify from what users see in the brief.

5. Data layer recommendation:
   Best database for MVP? For production?
   Is a cache needed? Is a job queue needed?
   Details will be finalised in Step 3.5.

6. Highest single technical risk in this project?

7. Any requirements in the brief that conflict with each other?

Wait for my confirmation before proceeding.
```

---

## Step 1 — Repo Setup [BOTH]

**Mode: Agent**

### Backend — always created

```
Create the full project structure.
Skeleton files with docstrings only — no implementation code.

Root:
  pyproject.toml
  .env.example            (every env var with description, no real values)
  .gitignore              (Python + venv + .env + __pycache__ + .DS_Store)
  README.md               (project name and one-line description)
  alembic.ini
  Makefile                (make dev, make test, make lint, make migrate)
  Dockerfile
  docker-compose.yml      (backend + postgres for local dev)

.github/
  copilot-instructions.md (fill from template at bottom of this doc)
  workflows/
    ci.yml                (fill from Step 12 — leave empty if MVP)

docs/
  project-brief.md        (already exists — do not overwrite)
  requirements.md         (empty)
  architecture.md         (empty)
  data-layer.md           (empty)
  ai-system-design.md     (empty)
  design.md               (empty)
  lessons-learned.md      (empty — updated after every mistake)
  decisions/
    adr-template.md       (standard ADR format)
  api-contracts/
    openapi.yaml          (empty OpenAPI 3.0 skeleton)

backend/
  app/
    __init__.py
    main.py               (FastAPI app init, router registration, CORS)
    api/
      __init__.py
      v1/
        __init__.py
        router.py
        endpoints/
          __init__.py
    core/
      __init__.py
      config.py           (pydantic-settings BaseSettings — all config)
      security.py         (JWT create/verify)
      logging.py          (structlog JSON config)
      exceptions.py       (all named exception classes)
      middleware.py       (request ID, CORS, global error handler)
    models/
      __init__.py
    schemas/
      __init__.py
    services/
      __init__.py
    repositories/
      __init__.py
    ai/
      __init__.py
      client.py           (AI API client: retry, timeout, cost logging)
      prompts/
        __init__.py
        main_prompt.py    (PROMPT_VERSION + templates)
  migrations/
    versions/
    env.py

tests/
  __init__.py
  conftest.py
  unit/
    __init__.py
  integration/
    __init__.py
  prompts/
    test_cases.json
    run_evals.py

pyproject.toml — fill with:

[tool.poetry.dependencies]
python = "^3.12"
fastapi = "^0.115"
uvicorn = {extras = ["standard"], version = "^0.30"}
pydantic = "^2.7"
pydantic-settings = "^2.3"
sqlalchemy = "^2.0"
alembic = "^1.13"
structlog = "^24.1"
httpx = "^0.27"
tenacity = "^8.3"
python-jose = {extras = ["cryptography"], version = "^3.3"}
passlib = {extras = ["bcrypt"], version = "^1.7"}
redis = "^5.0"     (remove if MVP)
celery = "^5.4"    (remove if MVP)

[tool.poetry.group.dev.dependencies]
pytest = "^8.2"
pytest-asyncio = "^0.23"
pytest-cov = "^5.0"
ruff = "^0.5"
mypy = "^1.10"
black = "^24.4"
bandit = "^1.7"
safety = "^3.2"

[tool.ruff]
line-length = 88
select = ["E", "F", "I", "N", "W", "UP", "ASYNC"]

[tool.mypy]
strict = true
ignore_missing_imports = true

[tool.pytest.ini_options]
asyncio_mode = "auto"
testpaths = ["tests"]
addopts = "--cov=backend --cov-report=term-missing --cov-fail-under=80"
```

### Frontend — only if SCOPE: fullstack

```
Also create:

frontend/
  package.json
  tsconfig.json           (strict TypeScript)
  vite.config.ts          (proxy /api to localhost:8000)
  .env.example            (VITE_API_BASE_URL=http://localhost:8000)
  index.html
  src/
    main.tsx
    App.tsx
    api/
      client.ts           (axios instance, auth headers, base URL from env)
      types.ts            (generated from openapi.yaml — do not edit by hand)
    components/
      ui/
    features/
    hooks/
    stores/
    utils/
      stream.ts           (SSE reader for AI streaming responses)

frontend/package.json dependencies:
  react, react-dom, react-router-dom
  axios, @tanstack/react-query, zustand
  typescript, vite, @vitejs/plugin-react
  @testing-library/react, vitest
  eslint, prettier, openapi-typescript
```

---

## Step 2 — Requirements [BOTH]

**Mode: Plan**

### MVP version [MVP]

```
Read #docs/project-brief.md

Write a lean requirements doc directly to docs/requirements.md.
One page max. Bullet points. Write immediately — no approval needed.

Cover only:
1. What the system must do (5-10 numbered requirements)
2. What it must NOT do (explicit scope boundary)
3. The one performance requirement that matters for the demo
4. Security minimum: is auth required, is input validated

Mark unknowns as [TBD]. Decide later.
```

### Production version [PROD]

```
Read #docs/project-brief.md

Propose a complete requirements document. Do NOT write yet.

1. Functional requirements
   Numbered. Format: "The system SHALL [testable behaviour]"
   Grouped: Core features / AI features / Auth / Admin

2. Non-functional requirements (specific measurable targets)
   Latency: p50 / p95 / p99 targets
   AI response: time to first token / total time
   Availability: uptime percentage and downtime per month
   Throughput: requests per second
   Cost: max per request and per user per month

3. AI-specific requirements
   Max tokens per request (input + output combined)
   Behaviour when model is unavailable
   Cost ceiling per user per day
   Inputs that must be sanitised before the model

4. Security requirements
   Auth method and session lifetime
   PII fields and handling
   Rate limiting thresholds
   What must never appear in logs

5. Out of scope — explicit list

6. Success metrics — numbers only

7. Open decisions needing an ADR

Propose first. After approval: write docs/requirements.md
```

---

## Step 3 — Architecture [BOTH]

**Mode: Plan**

### MVP version [MVP]

```
Read #docs/requirements.md

Propose a minimal architecture. Plain text. Under one page. No alternatives.

Cover:
- Main components (2 sentences each)
- Database: SQLite is fine for MVP — say so or flag if not appropriate
- API: REST, v1, JWT auth
- Sync vs async split
- The one failure mode that kills the MVP demo

Write directly to docs/architecture.md. No approval needed.
```

### Production version [PROD]

```
Read #docs/requirements.md and #docs/project-brief.md

Propose the system architecture. Propose first, write after approval.

1. System components — name, responsibility, technology with justification
2. API design — REST vs GraphQL, auth, versioning, error response format
3. Async and background jobs — what, which queue, retry strategy
4. Failure handling — DB down, AI API down, circuit breaker decision
5. Scaling ceiling — when does this break, what is the first bottleneck

Alternative approach with tradeoff table:
| Factor           | Chosen | Alternative |
|------------------|--------|-------------|
| Complexity       |        |             |
| Performance      |        |             |
| Cost             |        |             |
| Maintainability  |        |             |

After approval: write docs/architecture.md and docs/decisions/ ADR files.
```

---

## Step 3.5 — Data Layer Strategy [BOTH]

**This step was missing from the original. Every AI app needs a deliberate
data strategy or you face expensive rewrites when you try to scale.**

**Mode: Plan**

```
Read #docs/requirements.md, #docs/architecture.md, #docs/project-brief.md

Design the complete data layer. Answer each section with a justified recommendation.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. PRIMARY DATABASE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

MVP:        SQLite — zero infrastructure, file-based, good to ~10k users
Production: Postgres — concurrent writes, JSONB, pgvector, mature tooling

Migration path: SQLite to Postgres is connection string only if SQLAlchemy
is used throughout. Confirm this is the approach.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
2. VECTOR DATABASE — decision tree
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Answer YES or NO to each:
- Do you need semantic search over documents?
- Do you need context retrieval for RAG?
- Do you need similarity search by meaning?

If all NO: skip vector DB. Use Postgres full-text search.
If any YES, choose one and justify:

  pgvector   → inside Postgres, good to ~1M vectors, zero extra infra
               Best when: already using Postgres, vectors are secondary
  Chroma     → local, open source, zero cost
               Best when: MVP, prototyping, local development
  Pinecone   → managed, scales to billions, has real cost
               Best when: large-scale production, prefer managed services
  Weaviate   → managed or self-hosted, good multi-modal support
               Best when: text + images + audio in one search

Justify based on: expected vector count at launch, at one year, budget.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
3. CACHING STRATEGY — decision tree
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Answer YES or NO:
- Can same input produce same AI response? (deterministic prompt)
- High-volume reads on rarely-changing data?
- Need rate limiting or sessions that survive restarts?

If all NO: no cache in MVP. Add only when you have evidence.

If YES to "same input → same output":
  Cache AI responses — largest cost saving possible.
  Cache key: hash(model_name + prompt_version + sanitised_input)
  TTL: based on how often source data changes.

MVP cache:  cachetools in-memory dict — zero infrastructure
Production: Redis — survives restarts, multi-instance, rate limiting

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
4. BACKGROUND JOB QUEUE — decision tree
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Answer YES or NO:
- Any user operation takes more than 2 seconds?
- Do you send emails, notifications, or webhooks?
- Do you ingest or embed documents?
- Do you run batch AI processing?

If all NO: FastAPI BackgroundTasks, no infrastructure needed.

MVP:  FastAPI BackgroundTasks — no infra, handles low volume
Prod: Celery + Redis — full retry, priorities, monitoring dashboard

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
5. CONVERSATION STORAGE (chat AI apps only)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Storage: database table (queryable, persistent, auditable)
Max turns: [N] — balance context quality vs token cost
Retention: [how long before old conversations are deleted]

Schema:
  id UUID, user_id UUID, session_id UUID,
  role VARCHAR(20), content TEXT,
  created_at TIMESTAMPTZ, token_count INT

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
OUTPUT REQUIRED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Produce this decision table:

| Layer        | MVP choice       | Prod choice         | Migration effort |
|--------------|------------------|---------------------|-----------------|
| Primary DB   | SQLite           | Postgres            | Low             |
| Vector DB    | [none/Chroma]    | [pgvector/other]    | Medium          |
| Cache        | Python dict      | Redis               | Low             |
| Job queue    | BackgroundTasks  | Celery + Redis      | Medium          |
| Sessions     | [choice]         | [choice]            | Low             |

Propose and wait for my approval.
After approval: write docs/data-layer.md
```

---

## Step 3.6 — AI System Design [BOTH]

**Mode: Plan**

### MVP version [MVP]

```
Read #docs/project-brief.md and #docs/data-layer.md

Design a minimal AI system. Write directly to docs/ai-system-design.md.
No approval needed.

Cover:
- Which model and why (one sentence)
- Main prompt template skeleton (show the actual template text)
- How user input is sanitised before reaching the prompt
- What happens when model call fails (retry once, then error)
- Cost estimate: (input_tokens/1000 x price) + (output_tokens/1000 x price)

Also create backend/app/ai/prompts/main_prompt.py with:
  PROMPT_VERSION = "v1"
  PROMPT_UPDATED = "[today's date]"
  PROMPT_CHANGE_REASON = "Initial version"
  SYSTEM_PROMPT = """[prompt template with {variable} placeholders]"""
```

### Production version [PROD]

```
Read #docs/requirements.md, #docs/architecture.md, #docs/data-layer.md

Propose the complete AI system design. Propose first, write after approval.

1. Framework choice and version — justify over alternatives

2. Model configuration
   Primary model + fallback model
   Parameters: temperature, max_tokens, top_p — justify each value

3. Prompt design
   System / user / assistant structure
   Variables injected into templates
   Version strategy: PROMPT_VERSION in every file, bump on every change
   Main prompt skeleton with inline comments

4. Context management
   Token budget per section: system / history / retrieval / output
   Strategy when approaching limit
   Conversation history: turns to keep and storage location

5. RAG design (if vector DB chosen in Step 3.5)
   Chunking: size in tokens, overlap, split strategy
   Embedding model: name, dimensions, cost per call
   Retrieval: similarity threshold, top-k, reranking decision

6. Streaming (if required)
   FastAPI StreamingResponse with async generator
   Frontend SSE consumption
   Mid-stream error handling

7. Async design
   All AI calls via httpx AsyncClient
   No sync network calls inside async handlers

8. Safety and reliability
   Input sanitisation: control chars, length limit, injection patterns
   Output validation: Pydantic schema for expected shape
   Token budget: enforced before every call
   Timeout: 30 seconds default, configurable

9. Cost tracking
   Formula: (input_tokens/1000 x price_in) + (output_tokens/1000 x price_out)
   Table: ai_usage (user_id, model, input_tokens, output_tokens, cost_usd,
                    prompt_version, created_at)
   Daily budget per user enforced before every call

10. Prompt versioning
    PROMPT_VERSION in every prompt file
    Change comment on every update
    Old versions kept in git — never deleted

After approval: write docs/ai-system-design.md and AI module skeleton files.
```

---

## Step 3.7 — Frontend Design (Fullstack Only)

**Skip entirely if SCOPE: backend-only**

**Mode: Plan**

```
Read #docs/requirements.md, #docs/architecture.md, #docs/ai-system-design.md

Design the React/TypeScript frontend.

1. Route map — every page with its purpose
2. Component hierarchy — layout to feature level
3. State — global (zustand) vs local (useState)
4. API integration — axios, auth headers, error handling
5. AI streaming UX — typing effect, loading state, cancel button
6. Auth flow — login, logout, token refresh, protected routes
7. UX states per feature — loading / success / empty / error

Propose first. After approval: write docs/frontend-design.md and skeleton.
```

---

## Step 4 — Module Design [BOTH]

**Mode: Plan**

```
Read #docs/requirements.md, #docs/architecture.md,
#docs/data-layer.md, #docs/ai-system-design.md
and #docs/frontend-design.md (if exists)

Produce a module breakdown. Use this format for every module:

---
Module: [Name]
Layer: [API / Service / Repository / AI / Schema / Middleware / Frontend]
File: [exact file path]
Responsibility: [one sentence — what this module does and nothing else]
Public interface:
  - function_name(param: Type) -> ReturnType — description
Dependencies:
  - [Module] — why
  - [package] — why
Errors it raises: [named classes from core/exceptions.py]
Pydantic schemas: [schema names]
Phase: [1-Foundation / 2-Core / 3-Feature / 4-Integration]
Parallel-safe: [YES — no dependency on other Phase 3 modules]
              [NO — depends on: list what]
---

After all modules:
- Dependency graph
- Recommended build order
- Circular dependency risks
- Any module violating single responsibility

Propose first. After approval:
  write docs/design.md
  write backend/app/schemas/__init__.py (all shared Pydantic schemas)
  write backend/app/core/exceptions.py (all named exception classes)
```

---

## Step 5 — Implementation [BOTH]

**Mode: Agent — one file at a time without exception**

**Rule: implement one file, review it, fix it, then next file.**

### Implementation prompt — use for every file

```
Read #docs/design.md and these files for context:
  #backend/app/schemas/__init__.py
  #backend/app/core/exceptions.py
  #backend/app/core/config.py

Implement: [exact file path]
Module: [module name from design.md]

Write in this order:
1. Module docstring — purpose, what it does, what it does NOT do
2. Imports — stdlib / third-party / local (blank line between groups)
3. Pydantic schemas specific to this module
4. Named exceptions extending from core/exceptions.py
5. Core logic — async functions, pure where possible
6. Repository functions — all DB access isolated here
7. Structured logging — entry, success, every error path
8. Input validation at every public boundary

Python rules:
- Type annotations on every public function
- Google-style docstring on every public function and class
- async/await for all I/O
- Pydantic BaseModel for all data shapes at boundaries
- structlog for logging — no print(), no stdlib logging
- Config from app.core.config.settings — no hardcoded values
- Max 30 lines per function
- No mutable default arguments

After writing:
  Run: ruff check [filepath] — fix all errors
  Run: mypy [filepath] — fix all errors
Stop. Report file is ready for review. Do not implement any other file.
```

### After each file — dual-model review

MVP: review AI layer files and auth files only.
Production: review every file.

For AI layer files add to the reviewer prompt:

```
ADDITIONAL AI-SPECIFIC CHECKS:
- Is user input sanitised before prompt injection?
- Is there a token limit check before the model call?
- Is the model response validated with Pydantic before use?
- Is there retry with exponential backoff on transient failures?
- Is there a fallback when the primary model is unavailable?
- Is cost logged per call (input_tokens, output_tokens, cost_usd)?
- Is there a 30-second timeout on the model call?
- Can a user manipulate the prompt through crafted input?
```

---

## Step 6 — Testing [BOTH]

**Mode: Agent — run per module after it passes review**

### MVP version [MVP]

```
Read #[module file] and #tests/conftest.py

Write focused tests for [ModuleName].
Framework: pytest with pytest-asyncio.
Mock all external AI API calls — never hit real APIs in tests.

Write only:
1. Happy path for every public function
2. The one failure that would crash the demo
   (AI API unavailable is always the most important)
3. Auth tests if this module has protected endpoints

Run: pytest tests/ -v -x
Fix failures in implementation. Never modify a test to make it pass.
Report: X passing, Y failing.
```

### Production version [PROD]

```
Read #[module file] and #tests/conftest.py

Complete test suite for [ModuleName].
Framework: pytest, pytest-asyncio, httpx AsyncClient for API tests.
All AI API calls mocked — never hit real APIs in tests.

Round 1 — Unit tests (tests/unit/test_[module].py):
- Every public function: happy path + every failure path
- Edge cases: None, empty string, empty list, zero, max value
- Wrong types: confirm Pydantic rejects them

Round 2 — Integration tests (tests/integration/test_[module].py):
- Full request to response cycle
- DB operations with SQLite in-memory test DB
- Protected endpoints: valid / expired / missing token

Round 3 — AI-specific (if AI module):
- Normal input: assert response matches Pydantic schema
- Prompt injection attempt: assert blocked or sanitised
- AI API error: assert fallback activates correctly
- AI API timeout: assert timeout handled, not a crash
- Token limit exceeded: assert rejection before model call
- Unexpected response format: assert graceful error

Round 4 — Adversarial:
- Maximum allowed input size
- Concurrent calls to the same endpoint
- Malformed but schema-valid JSON

Run: pytest tests/ -v --cov=[module] --cov-fail-under=80
Fix bugs in implementation. Never delete a test to make count green.
Report: X passing, Y failing, coverage %.
```

### API contract validation — Fullstack only [BOTH]

```
Read #docs/api-contracts/openapi.yaml

For every endpoint in the spec:
1. Confirm FastAPI route exists with correct method
2. Confirm request schema matches
3. Confirm response schema matches
4. Confirm auth requirement is implemented

Undocumented routes: add to openapi.yaml.
openapi.yaml is the source of truth.

Run: npx openapi-typescript docs/api-contracts/openapi.yaml -o frontend/src/api/types.ts
Confirm no TypeScript errors in the generated file.
```

---

## Step 7 — Observability [PROD]

**MVP: SKIP. Add one structlog line per endpoint handler. That is enough.**

**Mode: Agent**

```
Read all files in #backend/app/

Add production observability. Edit existing files only.

1. Request logging — every request:

Entry:  { "event": "request_received", "method": "POST",
          "path": "/api/v1/...", "user_id": "...", "request_id": "uuid4" }

Exit:   { "event": "request_completed", "status_code": 200,
          "duration_ms": 145, "request_id": "same uuid" }

Error:  { "event": "request_failed", "error_type": "ValidationError",
          "error_message": "...", "duration_ms": 23, "request_id": "same uuid" }

2. AI call logging:

Success: { "event": "ai_call_completed", "model": "gpt-4o",
           "prompt_version": "v3", "input_tokens": 450, "output_tokens": 230,
           "cost_usd": 0.0068, "duration_ms": 1240, "request_id": "..." }

Failure: { "event": "ai_call_failed", "model": "gpt-4o",
           "error_type": "RateLimitError", "attempt": 2, "request_id": "..." }

3. Health check — GET /health:
{ "status": "healthy", "version": "1.0.0", "database": "connected",
  "ai_api": "reachable", "uptime_seconds": 3600 }

4. Request ID middleware:
Generate UUID4 per request, bind to structlog context,
return as X-Request-ID response header.

NEVER log:
- Passwords in any form
- API keys or bearer tokens
- Full prompt content (log prompt_version + hash instead)
- PII: name, email, phone — log user_id only

Run full test suite after all changes. Show every file modified.
```

---

## Step 8 — AI Evaluation Pipeline [PROD]

**MVP: SKIP. Manually test 5-10 inputs by hand before shipping.**

**Mode: Agent**

```
Read #docs/ai-system-design.md and #tests/prompts/

Build the complete AI evaluation pipeline.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. TEST CASE FORMAT — tests/prompts/test_cases.json
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{
  "id": "tc_001",
  "prompt_version": "v1",
  "description": "What this test verifies",
  "input": { "user_message": "...", "context": "..." },
  "expected": {
    "must_contain": ["phrase1", "phrase2"],
    "must_not_contain": ["bad_phrase"],
    "format": "json_object | plain_text | code_block",
    "max_tokens": 500,
    "quality_score_min": 3
  },
  "tags": ["happy_path", "edge_case", "security"],
  "source": "manual | user_feedback",
  "created": "2024-01-15"
}

Start with 10 cases: 3 happy path, 3 edge cases, 2 refusals, 2 injection attempts.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
2. EVALUATION RUNNER — tests/prompts/run_evals.py
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Script that:
- Loads all test cases
- Calls AI with the specified prompt version
- Scores each:
  PASS: all must_contain found, no must_not_contain, format correct
  FAIL: any condition violated
- Quality score 1-5 using GPT-4o or Claude as judge:
  5=perfect, 4=good, 3=acceptable, 2=weak, 1=wrong/harmful
- Output table:
  tc_001 | PASS  | score: 4/5 | 0.23s | 145 tokens | $0.002
  tc_002 | FAIL  | score: 1/5 | must_contain "summary" not found
- Summary: X/Y passed, avg quality Z/5, total cost $A, avg latency Bms
- Exit code 0 if all pass, exit code 1 if any fail

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
3. REGRESSION DETECTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Run: python tests/prompts/run_evals.py --compare v1

Output:
  tc_001 | v1: PASS score:4 | v2: PASS score:4  | stable
  tc_002 | v1: PASS score:4 | v2: FAIL score:1  | REGRESSION

Rule: any PASS to FAIL is a regression — block the prompt change.
Rule: quality score drop of 2+ points also counts as regression.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
4. FEEDBACK-TO-TEST BRIDGE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

When user gives thumbs down (from Step 9):
- Retrieve original request from logs using request_id
- Auto-create draft test case: "source": "user_feedback", "status": "draft"
- You review and add must_contain / must_not_contain
- Approved draft becomes an active eval test case

Loop: user complaint → test case → prompt fix → regression test
```

---

## Step 9 — User Feedback Loop [BOTH]

**This step was completely missing. Real AI apps improve through user feedback.
Without collecting it, you are guessing what to fix.**

**Mode: Agent**

### MVP version [MVP]

```
Add thumbs up / thumbs down feedback.

1. Migration — add ai_feedback table:

CREATE TABLE ai_feedback (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     UUID REFERENCES users(id) ON DELETE CASCADE,
  request_id  VARCHAR(36) NOT NULL,
  rating      SMALLINT NOT NULL CHECK (rating IN (1, 5)),
  created_at  TIMESTAMPTZ DEFAULT NOW()
);
CREATE INDEX ON ai_feedback (user_id);
CREATE INDEX ON ai_feedback (rating, created_at);

2. Endpoint — POST /api/v1/feedback:
  Request:  { "request_id": "string", "rating": 1 }
            1 = thumbs down, 5 = thumbs up
  Response: { "message": "Feedback recorded" }
  Auth: required

3. Frontend (if fullstack):
  Thumbs up / thumbs down below every AI response.
  On click: POST to /api/v1/feedback with request_id from X-Request-ID header.
  Show "Thanks!" on success. No page reload.

4. Admin query:
  SELECT rating, COUNT(*) as count, ROUND(AVG(rating), 2) as avg
  FROM ai_feedback
  WHERE created_at > NOW() - INTERVAL '7 days'
  GROUP BY rating ORDER BY rating DESC;
```

### Production additions [PROD]

```
5. Extend the table:
  ALTER TABLE ai_feedback ADD COLUMN comment TEXT;
  ALTER TABLE ai_feedback ADD COLUMN prompt_version VARCHAR(10);
  ALTER TABLE ai_feedback ADD COLUMN input_hash CHAR(64);

6. Negative feedback alert:
  If thumbs down rate in last 1 hour exceeds 20%:
  Log WARNING: { "event": "high_negative_feedback_rate", "rate": 0.23 }
  Send alert to admin (email or Slack webhook).

7. Feedback to eval case:
  When rating = 1 (thumbs down):
  - Retrieve original request from logs via request_id
  - Create draft in test_cases.json:
    { "source": "user_feedback", "status": "draft", "input": <original> }
  - You complete the expected section and approve it
  - Approved drafts become active eval test cases

8. Weekly report:
  Every Monday: query last 7 days of ai_feedback
  Calculate: total, thumbs up %, thumbs down %, by prompt version
  Write to docs/feedback-reports/week-YYYY-MM-DD.md

9. Retraining signal:
  Export thumbs down with comments to docs/feedback-reports/negative-examples.jsonl
  Format: { "input": "...", "bad_output": "...", "comment": "..." }
  Use when creating new prompt test cases and for future fine-tuning.
```

---

## Step 10 — Deployment Strategy [BOTH]

**This step was completely missing. Code that never ships has zero value.**

**Mode: Plan then Agent**

### Step 10.1 — Choose hosting [Plan]

```
Read #docs/requirements.md and #docs/project-brief.md

Recommend a hosting strategy based on:
SCOPE: [fullstack / backend-only]
MODE: [mvp / production]
Expected users at launch: [from brief]
Budget: [from brief]
Compliance: [from brief]

For each option: monthly cost estimate, setup time, scalability ceiling.

MVP options:
  Railway     → zero DevOps, GitHub push deploys, $5-20/month, good to ~10k users
  Render      → similar to Railway, free tier available (slow cold starts)
  Fly.io      → more control, global edge, $0-10/month for small apps
  Supabase    → best if already using Supabase for DB and auth

Production options:
  AWS (ECS + RDS)    → most control, cost-effective at scale, most DevOps
  GCP (Cloud Run)    → serverless containers, pay per request, auto-scales
  Hetzner + Dokku    → cheapest, EU-based, moderate DevOps required

Recommend one for MVP and one for production. Justify each.
Wait for my approval before configuring anything.
```

### Step 10.2 — Configure deployment [Agent]

```
Configure deployment for [chosen platform].

1. Dockerfile — production container:

FROM python:3.12-slim AS builder
WORKDIR /app
COPY pyproject.toml poetry.lock ./
RUN pip install poetry==1.8 && \
    poetry config virtualenvs.create false && \
    poetry install --only=main --no-root

FROM python:3.12-slim
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.12 /usr/local/lib/python3.12
COPY --from=builder /usr/local/bin /usr/local/bin
COPY backend/ ./backend/
EXPOSE 8000
CMD ["uvicorn", "backend.app.main:app", "--host", "0.0.0.0", "--port", "8000"]

2. docker-compose.yml — local development:

services:
  backend:
    build: .
    ports: ["8000:8000"]
    env_file: .env
    volumes: ["./backend:/app/backend"]
    depends_on:
      db:
        condition: service_healthy
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: devpassword
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser"]
      interval: 5s
      retries: 5

3. Start command — run migrations on every deploy:
   alembic upgrade head && uvicorn backend.app.main:app --host 0.0.0.0 --port 8000

4. Secrets strategy:
   MVP:  Set in Railway/Render dashboard manually. Document in .env.example.
   Prod: Platform secrets manager. Rotate quarterly — calendar reminder.

5. Health check:
   Platform hits GET /health every 30 seconds.
   3 consecutive failures = failed deploy.

6. Rollback plan:
   Every deploy tagged in git.
   To rollback: redeploy previous version tag.
   DB rollback: alembic downgrade -1
   Add Makefile target: make rollback

PRODUCTION ONLY:

7. Multi-environment:
   staging: auto-deploy from develop branch
   production: auto-deploy from main only, staging must be green first

8. Scaling:
   Minimum 2 instances (eliminate single point of failure)
   Auto-scale: CPU > 70% or memory > 80%
   DB connection pooling: max 20 connections per instance

9. Monitoring:
   Uptime: UptimeRobot or BetterStack (free tiers)
   Alerts: downtime > 1 min, error rate > 5%, response > 5s
   AI cost: weekly dashboard report, alert at daily spend threshold

10. Backups:
    Automated daily. Retention: 7 days MVP, 30 days production.
    Monthly restore test — confirm backup actually works.
```

---

## Step 11 — Final Code Review [BOTH]

**Mode: Plan then Agent**

### Plan mode — get full report first

```
Read all files changed in this session.

Review as a principal engineer before deployment.

Format every issue:
[CRITICAL] file:line — what is wrong — exact fix
[HIGH]     file:line — what is wrong — exact fix
[MEDIUM]   file:line — what is wrong — note
[LOW]      file:line — what is wrong — note

Check:
CORRECTNESS     — logic errors, wrong async patterns, missing returns
PYTHON          — missing type annotations, docstrings, anti-patterns
AI SAFETY       — prompt injection, unvalidated output, cost tracking
SECURITY        — unprotected endpoints, PII in logs, hardcoded secrets
ARCHITECTURE    — service importing service, business logic in routes
DEPLOYMENT      — hardcoded values that should be env vars

Changed files only. Show full report. Do not fix yet.
```

### Agent mode — after you decide what to fix

```
Fix all CRITICAL issues now.
[Production: also fix all HIGH issues.]

Rules:
- Fix only what is in the review report
- Do not change anything not flagged
- Run full test suite after every fix
- Show a diff of every change
```

---

## Step 12 — CI/CD Pipeline [BOTH]

**Mode: Agent**

### MVP [MVP]

```
Create .github/workflows/ci.yml

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: '3.12' }
      - run: pip install poetry && poetry install
      - run: poetry run pytest tests/ -v -x --tb=short
```

### Production [PROD]

```
Create .github/workflows/ci.yml

Trigger: push and pull_request to main and develop

Job 1 — code-quality (blocks everything):
  ruff check backend/ — zero errors
  mypy backend/ — zero errors
  black --check backend/

Job 2 — backend-tests (needs: code-quality):
  services: postgres:16
  pytest tests/ -v --cov=backend --cov-fail-under=80 --cov-report=xml
  upload coverage artifact

Job 3 — security-scan (parallel with backend-tests):
  bandit -r backend/ -ll — fail on medium severity
  safety check — fail on known vulnerabilities

Job 4 — ai-evaluation (needs: backend-tests):
  python tests/prompts/run_evals.py
  fail if any PASS to FAIL regression detected

Job 5 — frontend-quality (parallel, fullstack only):
  cd frontend && npm ci
  npm run lint — zero errors
  npm run type-check
  npm run test
  npm run build

Job 6 — build-and-push (needs: backend-tests, security-scan):
  Build Docker image
  Smoke test: GET /health returns 200
  Push to registry on main only

Job 7 — deploy-staging (needs: build-and-push, main only):
  Deploy to staging
  Smoke test against staging /health
  If fails: rollback and fail pipeline
```

---

## Step 13 — Iteration Loops [BOTH]

### Code iteration loop — every feature change

```
1. Update docs/project-brief.md if scope changes
2. Update docs/requirements.md
3. Update docs/architecture.md if structure changes
4. Update docs/design.md
5. Implement (Step 5 — one file, review, fix, next file)
6. Run tests — fix regressions in implementation, not tests
7. Step 11 review — fix Critical (+ High in production)
8. Commit: git commit -m "feat(module): what changed and why"
9. Push — confirm CI passes before calling it done
```

### Prompt iteration loop — every AI behaviour change

```
1. Write the test case FIRST:
   { "input": "exact input that showed the problem",
     "must_contain": ["what good output includes"],
     "must_not_contain": ["what bad output had"] }
   Add to tests/prompts/test_cases.json

2. Run evals to establish baseline:
   python tests/prompts/run_evals.py

3. Make the prompt change

4. Bump version: PROMPT_VERSION = "v[N+1]"
   Add change comment: # v[N+1] — what changed and why

5. Run regression detection:
   python tests/prompts/run_evals.py --compare

6. Any regression: revert, redesign, repeat from step 3
   No regressions: commit the new version

7. Update test_cases.json to cover new behaviour
```

---

## AI App Rules

Applied to every AI layer file. The reviewer checks all of these.

```
PROMPT SAFETY

Rule 1: User input NEVER concatenated directly into prompt strings.
        BAD:  prompt = f"Analyse: {user_input}"
        GOOD: prompt = TEMPLATE.format(content=sanitise(user_input))

Rule 2: Sanitise before injection:
        Strip control characters, limit length, check for injection patterns
        ("ignore previous instructions", "system:", "DAN", etc.)

Rule 3: Validate model response with Pydantic before using.
        Never assume the model returned the expected format.
        Never execute code or commands from model output.

RELIABILITY

Rule 4: Every AI call has a timeout. Default 30s. Configurable in settings.

Rule 5: Retry with exponential backoff on transient errors.
        Retry:    RateLimitError, ConnectionError, Timeout
        No retry: AuthenticationError, InvalidRequestError

Rule 6: Every endpoint has a fallback when primary model is unavailable.

Rule 7: Token budget enforced before every call.
        Over budget: return clear error, do not call the model.

COST

Rule 8: Every AI call logs: input_tokens, output_tokens, cost_usd, prompt_version.
        Formula: (input_tokens/1000 x price_in) + (output_tokens/1000 x price_out)

DATA SAFETY

Rule 9: NEVER log: passwords, API keys, full prompt text, PII fields.

Rule 10: Code that includes PII in prompts must have comment:
         # PII: this prompt may include personal data — review retention policy

VERSIONING

Rule 11: Every prompt file has PROMPT_VERSION constant. Bump on every change.

Rule 12: Every prompt version has test cases. Update cases before changing prompt.
```

---

## Copilot Instructions File

Create .github/copilot-instructions.md — read at every session start.

```markdown
# Copilot instructions — [Project Name]

You are a staff-level Python engineer working on an AI application.
Before writing any code, read:
  docs/requirements.md
  docs/architecture.md
  docs/design.md

## Stack
Backend:  Python 3.12, FastAPI, SQLAlchemy, Alembic, Pydantic v2, structlog
AI:       [framework from Step 0.1]
Testing:  pytest 8, pytest-asyncio, httpx
Quality:  ruff, mypy (strict), black
Frontend: React 18, TypeScript 5, Vite, zustand [remove if backend-only]

## Code rules — every file
- Type annotations on every public function
- Google-style docstring on every public function and class
- async/await for all I/O — no sync calls in async context
- Pydantic BaseModel for all data shapes at boundaries
- structlog for logging — no print(), no stdlib logging
- Config from app.core.config.settings — no hardcoded values
- Max 30 lines per function
- No mutable default arguments

## AI rules — AI layer files only
- User input sanitised before every prompt injection
- Every AI call has timeout (30s) and exponential backoff retry
- Model response validated with Pydantic before use
- Cost logged per call: input_tokens, output_tokens, cost_usd
- Prompt version bumped on every update

## Error handling
- Named exception classes from app.core.exceptions — never raise Exception
- Every exception caught and logged at ERROR level
- API errors returned in RFC 7807 format

## Security
- Pydantic validation on all request input at API layer
- Auth checked before any business logic
- Never log: passwords, API keys, PII, full prompt content

## Architecture
- Services: business logic only — no direct DB calls
- Repositories: all DB access — nowhere else
- API routes: receive, call service, return — no logic

## Self-review before reporting done
- Does every function do exactly one thing?
- Is there any path where an exception goes unhandled?
- Is any external service call missing error handling and retry?

## After any correction
Add to docs/lessons-learned.md:
  Mistake: [what went wrong]
  Rule: [what prevents it next time]
```

---

## Quick Reference Card

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DECIDE FIRST
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SCOPE: fullstack  OR  backend-only
MODE:  mvp        OR  production

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WORKFLOW
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Step 0     Project brief                [BOTH]  you write manually
Step 0.1   Scope + mode                 [BOTH]  Plan confirm
Step 1     Repo setup                   [BOTH]  Agent
Step 2     Requirements                 [BOTH]  Plan Agent
Step 3     Architecture                 [BOTH]  Plan Agent
Step 3.5   Data layer strategy          [BOTH]  Plan Agent  ← NEW
Step 3.6   AI system design             [BOTH]  Plan Agent
Step 3.7   Frontend design              [BOTH]  Plan Agent (fullstack only)
Step 4     Module design                [BOTH]  Plan Agent
Step 5     Implementation               [BOTH]  Agent + dual review per file
Step 6     Testing                      [BOTH]  Agent per module
Step 7     Observability                [PROD]  Agent
Step 8     AI evaluation pipeline       [PROD]  Agent        ← NEW
Step 9     User feedback loop           [BOTH]  Agent        ← NEW
Step 10    Deployment strategy          [BOTH]  Plan Agent   ← NEW
Step 11    Final code review            [BOTH]  Plan Agent
Step 12    CI/CD pipeline               [BOTH]  Agent (simplified if MVP)
Step 13    Iteration loops              [BOTH]  forever

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MVP FAST TRACK — 40% of workflow
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Skip:     Step 7, Step 8, full Step 12, ADR docs
Fast:     Requirements (1 page), Architecture (rough text)
Fast:     Data layer (SQLite only), Deployment (Railway one push)
Review:   AI files + auth files only, Critical issues block only
Test:     Happy path + the failure that kills the demo
Feedback: Thumbs up/down only
Never:    Hardcoded secrets, missing input validation,
          no AI error handling, no working deployment

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PER-FILE RULE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Copilot writes one file
Reviewer (Claude or ChatGPT) reviews with reviewer prompt
Copilot fixes Critical (+ High if production)
VERDICT: PASS → next file
Never implement 2 files before reviewing 1

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SEVERITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[CRITICAL]  blocks both MVP and production
[HIGH]      blocks production only
[MEDIUM]    fix in session, does not block
[LOW]       note for later, does not block
MVP PASS  = zero Critical
PROD PASS = zero Critical + zero High

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DATA LAYER QUICK DECISIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Primary DB   SQLite (MVP) → Postgres (prod) — connection string only
Vector DB    Only if RAG or semantic search needed
             pgvector / Chroma / Pinecone — choose based on scale
Cache        Python dict (MVP) → Redis (prod)
             When: same input produces same AI output (cost saving)
Job queue    BackgroundTasks (MVP) → Celery+Redis (prod)
             When: any operation over 2 seconds

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DEPLOYMENT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MVP:   Railway or Render — GitHub push → auto deploy
       Migrations on startup: alembic upgrade head
       Secrets: platform dashboard only — never in git
Prod:  Docker + managed Postgres + secrets manager
       Staging before production — always
       Rollback: redeploy previous git tag
       Backups: daily automated, monthly restore test

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
AI EVALUATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MVP:   Manual — test 5-10 inputs by hand before shipping
Prod:  Automated pipeline with 1-5 quality scoring and regression detection
Rule:  Write test case BEFORE changing the prompt
Rule:  PASS to FAIL is a regression — do not ship

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
USER FEEDBACK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MVP:   Thumbs up/down — store in SQLite
Prod:  + comments, negative feedback alert at 20% rate,
         auto-generate eval cases from thumbs down
Loop:  thumbs down → draft test case → prompt fix → regression test

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
GRADUATION RULE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Start MVP. Graduate when: 50+ users / charging money /
sensitive data / risk of real harm.
Do not graduate early. Shipping beats perfecting.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

*Python + FastAPI + React/TypeScript + OpenAI/Anthropic/LangChain*
*MVP in days. Production when it matters.*

---


---

# Part 2 — Feature Change in Existing Project

Feature changes in existing projects are **always production-grade**.
There is no MVP mode here.

```
New product:    Unvalidated idea → MVP makes sense → ship fast, learn
Feature change: Production system with real users → only one standard → do it right
```

If you are making a feature change, you already have a live system.
Running a "lightweight" process on production code is not speed — it is risk.
A careless change breaks real users, creates real support tickets, and
costs more time to fix than the process would have taken.

**The scale of the process matches the size of the change — not a separate MVP mode.**

```
Small change  → lean version of each step, same steps
Medium change → standard version of each step
Large change  → full version plus feature flag and phased rollout
```

You classify the change size at the very start. That single decision scales the workflow.

---

## Change Size Classification

Run this before anything else. It takes 2 minutes and determines everything.

**Mode: Ask**

```
I want to make this change to the existing codebase:

[describe the change in one paragraph]

Classify this change as Small, Medium, or Large based on:

Small:
- Touches 1-3 files
- No new endpoints, no schema changes, no new dependencies
- No change to public API contracts
- Can be fully described in 5 bullet points
- Example: fix a bug, update a config value, add a log line

Medium:
- Touches 4-10 files
- May add one new endpoint or one new DB column
- Existing API contracts unchanged or additive only
- Can be fully described in 10-15 bullet points
- Example: add a new feature to an existing module, extend an existing endpoint

Large:
- Touches 10+ files, OR
- Adds multiple new endpoints, OR
- Changes existing API contracts (breaking or non-breaking), OR
- Requires a database migration affecting existing data, OR
- Adds a new dependency or new external service, OR
- Changes AI prompt behaviour or adds a new AI call
- Example: new module, significant refactor, new integration

State the classification and the primary reason for it.
Do not start any implementation.
```

Use this table to know what each size means for the workflow:

```
┌─────────────────────────────┬─────────┬────────┬───────┐
│ Step                        │ Small   │ Medium │ Large │
├─────────────────────────────┼─────────┼────────┼───────┤
│ Baseline check              │ ✓       │ ✓      │ ✓     │
│ Map the codebase            │ skip    │ ✓      │ ✓     │
│ Impact analysis             │ quick   │ ✓      │ ✓     │
│ Propose and approve plan    │ ✓       │ ✓      │ ✓     │
│ Atomic change rule          │ ✓       │ ✓      │ ✓     │
│ Diff size gate              │ 1 pass  │ 2 pass │ 3 pass│
│ Backward compat check       │ skip    │ ✓      │ ✓     │
│ Migration safety execution  │ skip    │ ✓      │ ✓     │
│ Feature flag                │ skip    │ skip   │ ✓     │
│ AI prompt risk gate         │ if AI   │ if AI  │ if AI │
│ Implement                   │ ✓       │ ✓      │ ✓     │
│ Performance impact check    │ skip    │ ✓      │ ✓     │
│ Existing tests pass         │ ✓       │ ✓      │ ✓     │
│ New tests                   │ ✓       │ ✓      │ ✓     │
│ Review the diff             │ quick   │ ✓      │ ✓     │
│ Pre-commit safety check     │ ✓       │ ✓      │ ✓     │
│ Final summary               │ ✓       │ ✓      │ ✓     │
│ Staging verification        │ skip    │ ✓      │ ✓     │
│ Code rollback plan          │ skip    │ ✓      │ ✓     │
└─────────────────────────────┴─────────┴────────┴───────┘
```

---

## Before Opening Copilot

**You do this manually — no Copilot involved yet.**

```bash
# 1. Create a branch — never work on main directly
git checkout -b feature/<your-feature-name>

# 2. Confirm clean baseline — nothing uncommitted
git status        # must show: nothing to commit, working tree clean

# 3. Confirm all tests pass before you touch anything
pytest            # must be fully green
# or: python -m pytest -v
```

**If tests are already failing — stop.**
Fix the baseline first. Never start a feature change on a broken codebase.
You will not know later whether you broke something or it was already broken.

---

## FC Step 0 — Baseline Check and No-Test Scenarios

Run your test command and check what you see:

```
pytest
  ↓
What do you see?
  ↓
"collected 0 items"        "X passed"              "X failed"
"no tests ran"                 ↓                       ↓
      ↓                Clean baseline            Fix these first
Go to Scenario A         Continue to FC Step 1    before touching anything
```

### Scenario A — No test suite exists at all

This changes your workflow. You cannot skip straight to implementing the feature.
Without tests, agent mode is flying blind. One wrong edit, no way to detect it.
You need a safety net first.

**Mode: Ask**

```
Read the entire backend/ folder (or src/ — wherever the Python code lives).

For every public function and class you find, describe:
- What it accepts as input
- What it returns
- What exceptions it can raise
- What side effects it has (DB writes, API calls, external services)

Do not suggest any changes.
I need to document current behaviour before writing any tests or changing any code.
```

**Mode: Agent**

```
Based on the behaviour you just described,
generate characterisation tests for the entire codebase.

Characterisation tests capture CURRENT behaviour —
not correct behaviour, not ideal behaviour.
Whatever the code does right now is what the test asserts.
Even if the behaviour seems wrong, test it as-is.
We are creating a snapshot, not fixing bugs.

Python-specific rules:
- Use pytest as the framework
- If a function currently returns None for missing input,
  assert None — do not change it to raise an exception
- If a function has no error handling,
  test the happy path only — do not add error cases
- Use the simplest possible assertions
- Name every test: test_current_behaviour_<what_it_does>
  so it is clear these are snapshots, not specifications
- Use pytest fixtures for any DB or external service setup
- Mock all AI API calls — never hit real APIs in characterisation tests

Write tests into:
  tests/characterisation/test_<modulename>.py

After writing, run:
  pytest tests/characterisation/ -v

Every characterisation test must pass before we continue.
```

Once all characterisation tests pass:

```bash
git add .
git commit -m "test: add characterisation tests as safety net"
```

Now you have a baseline. Continue to FC Step 1.

### Scenario B — Test framework not installed at all

`pytest` returns `command not found`, or `pyproject.toml` has no test script.

**Mode: Ask**

```
Read pyproject.toml (or requirements.txt or setup.py) and the backend/ folder.

What test framework would best fit this project based on:
- The existing Python version and dependencies
- Whether async code exists (needs pytest-asyncio)
- Whether there is any existing test configuration

Recommend one framework only. Do not install anything yet.
```

**Mode: Agent**

```
Set up the test framework you recommended.

Install via poetry or pip as appropriate for this project.
Create or update pyproject.toml with:
  [tool.pytest.ini_options]
  asyncio_mode = "auto"
  testpaths = ["tests"]

Create tests/__init__.py
Create tests/conftest.py with a placeholder fixture

Run: pytest --collect-only
Confirm pytest can discover tests before writing any.
```

Then return to Scenario A above and generate characterisation tests.

---

## FC Step 1 — Map the Codebase

**Skip if: Small change AND you know this codebase well.**
**Required for: Medium and Large changes, or any Small change in unfamiliar code.**

**Mode: Ask**

```
Read the backend/ folder (or src/ — wherever the Python code lives).

Give me a complete codebase map:

1. Every module and what it does (one sentence each)

2. Naming conventions used consistently:
   - File names (snake_case?)
   - Function names (verb_noun pattern?)
   - Class names (PascalCase?)
   - Variable names

3. Error handling pattern:
   - Named exception classes or generic exceptions?
   - Where are exceptions caught vs propagated?
   - What is the error response format returned by the API?

4. Logging approach:
   - Which logger (structlog / stdlib logging / loguru)?
   - What format (JSON / plain text)?
   - What fields are logged on every request?

5. How external dependencies are accessed:
   - Database: ORM, raw SQL, or repository pattern?
   - AI APIs: which client library, where is the client created?
   - Cache: where and how is it used?

6. Test structure:
   - Where do tests live (tests/unit/, tests/integration/)?
   - What fixtures exist in conftest.py?
   - What is already mocked (AI APIs, DB, external services)?

Do NOT suggest any changes. Map only.
```

Read the output carefully. Correct anything Copilot got wrong.
If Copilot misunderstands your structure here, every step that follows produces wrong results.

---

## FC Step 2 — Impact Analysis

**Mode: Ask**

**Quick version for Small changes:**

```
I want to make this change: [describe it]

Which files will need to change and why?
Does this touch any public API contracts?
Does this require a DB migration?
What is the riskiest part?

Analysis only. No solutions.
```

**Full version for Medium and Large changes:**

```
I want to add this feature:

[describe your feature in plain language — be specific]

Example of too vague:   "add user authentication"
Example of specific:    "add JWT-based login endpoint that accepts email
                         and password, validates credentials against the
                         users table, and returns an access token valid
                         for 24 hours"

Based on what you just read about this codebase,
answer these questions without touching any files.

1. Which existing files will need to change?
   List every file and the exact reason it needs to change.

2. Which files are affected indirectly?
   Files that import from or call the files above.

3. Which existing tests cover the code being changed?
   List each test file and what it currently asserts.

4. Does this change require a database migration?
   If yes: what table, what columns, what type, nullable or not?
   What is the exact Alembic command to create the migration?

5. Does this change add, remove, or modify any API endpoints?
   If yes: what changes to openapi.yaml are needed?

6. Does this change affect any AI prompts or AI layer behaviour?
   If yes: which prompt files change and what exactly changes?

7. Does this change affect any PUBLIC API contracts that existing
   callers depend on? Specifically:
   - Are any existing request fields being removed or renamed?
   - Are any existing response fields being removed or renamed?
   - Are any previously optional fields becoming required?
   - Are any response types changing?

8. What is the riskiest part of this change?
   Where is the most likely place something silently breaks?

9. What is the estimated performance impact?
   - Does this add any new database queries to an existing hot path?
   - Does this add a new AI call or increase token usage?
   - Does this add latency to any existing endpoint?
   - Does this add memory usage for in-flight requests?

10. Is there any tech debt in the affected area
    that makes this harder than it should be?

Analysis only. No solutions yet.
```

Read this carefully. A feature that looks like one file often touches five.
Better to know now than after agent mode has already edited half the codebase.

---

## FC Step 3 — Propose and Approve the Plan

**Mode: Plan**

```
Based on your impact analysis, propose the exact change plan.

ATOMIC CHANGE RULE — enforce this before writing the plan:
This PR must represent exactly one logical change.
One logical change = one thing that can be described in a single sentence,
deployed independently, and rolled back independently if it causes a problem.

If the change you are planning contains more than one logical concern,
split them now. Do not continue until this PR is atomic.

Examples of non-atomic changes that must be split:
  Non-atomic: "add new AI endpoint AND fix existing validation bug"
  Split into: PR1 = fix validation bug, PR2 = add AI endpoint

  Non-atomic: "add user preference table AND migrate existing users"
  Split into: PR1 = add table with nullable column, PR2 = backfill data

  Non-atomic: "refactor auth module AND add new auth feature"
  Split into: PR1 = refactor only (no behaviour change), PR2 = new feature

Is this change atomic? If not, list the separate logical concerns
and I will decide how to split them before we continue.

If the change is atomic, proceed with the plan:

For each file that needs to change:
- What currently exists in that file
- What you will specifically add, change, or remove
- Why that change is needed
- Which Pydantic schemas are added or modified

Also tell me:
- The exact order you will make these changes
  (dependencies first — if File B imports from File A, change File A first)
- Total number of files to be changed and estimated lines changed
  (this determines how many diff-size review checkpoints we will use)
- Whether a DB migration is needed:
  Show the exact Alembic command:
  alembic revision --autogenerate -m "description"
  Show the expected migration file content
- Whether any AI prompts change:
  Show the before and after of any prompt template
  Confirm PROMPT_VERSION will be bumped
- Whether any public API contracts change:
  Show the before and after of any request/response schema
- What could go wrong and how you will handle it
- Whether a feature flag is needed:
  (required if: Large change, or touches AI behaviour,
   or changes are visible to users before fully tested)

Do NOT make any changes yet.
Show me the full plan and wait for my approval.
```

Review the plan carefully. Use Ask mode to clarify anything that looks wrong.

**Atomic change check** — before approving, confirm:

```
In one sentence: what is the single logical change this PR makes?
[write your sentence here]

If you cannot describe it in one sentence, it is not atomic.
Split it before approving.
```

Once satisfied, confirm explicitly:

```
Approved. Before you start:
Repeat back to me — which files will you change and in what order?
```

Make Copilot repeat the list back. This confirms it understood correctly
before a single file is touched.

---

## FC Step 3.5 — Backward Compatibility Check

**Skip if: Small change with no API or schema changes.**
**Required for: Medium and Large changes — always.**

**Mode: Ask**

```
Based on the approved plan, answer these backward compatibility questions.
Do not touch any files.

API CONTRACT COMPATIBILITY
1. For every existing API endpoint being modified:
   - What does the current request schema look like?
   - What will the new request schema look like?
   - Will existing callers sending the OLD request still get a valid response?
   - Or will they get a 422 validation error?

2. For every existing API endpoint being modified:
   - What does the current response schema look like?
   - What will the new response schema look like?
   - Will existing callers expecting the OLD response still work?
   - Or will fields be missing that they depend on?

DATABASE COMPATIBILITY
3. If there is a DB migration:
   - Can the migration run on the live database without downtime?
   - Does it add nullable columns only? (safe — existing rows get NULL)
   - Does it add NOT NULL columns without a default? (unsafe — existing rows fail)
   - Does it rename or delete columns? (breaking — existing queries fail)
   - What happens if the migration fails halfway through?

4. Is there any period during deployment where:
   - The old code runs against the new schema? (deploy overlap)
   - The new code runs against the old schema? (rollback scenario)
   - Both need to work for zero-downtime deploys?

AI BEHAVIOUR COMPATIBILITY
5. If AI prompts change:
   - Will the new prompt produce structurally different output?
   - If output format changes, will existing code parsing the output still work?
   - Are there any stored AI outputs that will be inconsistent with new format?

For every breaking change found:
- Describe the exact breakage
- Propose the fix (additive migration, versioned endpoint, deprecation period)
```

If backward compatibility issues are found, update the plan in FC Step 3 before
implementing anything.

### Data Migration Safety Execution

If the plan includes a database migration, the migration cannot simply run as
part of normal startup. Follow this exact sequence.

**Classify the migration first:**

```
SAFE migrations (can run directly on live database):
  ADD COLUMN with nullable=True and no default
  ADD TABLE (new table, no existing data affected)
  ADD INDEX (concurrent if Postgres — CREATE INDEX CONCURRENTLY)
  DROP INDEX

UNSAFE migrations (require the expand-contract pattern):
  ADD COLUMN with nullable=False and no server_default
  RENAME COLUMN
  CHANGE COLUMN TYPE
  DROP COLUMN
  DROP TABLE
  Any migration that rewrites existing row data
```

**For SAFE migrations — execute directly:**

```bash
# 1. Run the migration on staging first
alembic upgrade head

# 2. Verify staging works completely
pytest tests/ -v

# 3. Run on production
alembic upgrade head

# 4. Confirm application started correctly
curl https://your-app.com/health
```

**For UNSAFE migrations — use the expand-contract pattern:**

This requires three separate PRs deployed in sequence. Never attempt in one PR.

```
PR 1 — Expand (additive only, no data change)
  Goal: add the new structure alongside the old
  Example: ADD COLUMN new_email VARCHAR NULLABLE
  Deploy and verify: old code still works, new column exists but unused

PR 2 — Migrate (data change only, no schema change)
  Goal: populate the new structure with existing data
  Example: UPDATE users SET new_email = email WHERE new_email IS NULL
  Run as a background job or migration script, not in alembic
  Monitor row counts before and after
  Deploy and verify: both old and new columns have correct data

PR 3 — Contract (remove old structure)
  Goal: remove what is no longer needed
  Example: DROP COLUMN email (after new_email is in use everywhere)
  Only after code no longer references the old column
  Deploy and verify: application works with only the new structure
```

**Migration rollback — always confirm this before running on production:**

```bash
# Test the rollback works BEFORE running upgrade on production
# Do this in staging:
alembic upgrade head   # run the migration
alembic downgrade -1   # confirm it reverses cleanly
alembic upgrade head   # run it again to confirm idempotent

# If downgrade fails in staging — fix the migration before touching production
```

---

## FC Step 3.6 — Feature Flag Strategy

**Skip if: Small or Medium change.**
**Required for: Large changes, any change to visible AI behaviour, any phased rollout.**

**Mode: Plan**

```
Based on the approved plan, design a feature flag strategy.

Answer:

1. What exactly does the flag control?
   What is ON when the flag is true, and what is OFF when it is false?
   Write it as: "When FEATURE_<NAME>=true: [behaviour]. When false: [behaviour]."

2. Where does the flag live?
   Environment variable (simplest — change in platform dashboard, restart required)
   Database row (no restart required, change takes effect immediately)
   In-memory config (fastest, but lost on restart)
   For most cases: environment variable is correct.

3. What is the default value?
   New features should default to OFF (false) until explicitly enabled.
   This means deploying the code does NOT activate the feature.

4. Show the exact implementation:
   In backend/app/core/config.py add:
     feature_<name>_enabled: bool = False
   In the feature code:
     from app.core.config import settings
     if not settings.feature_<name>_enabled:
         raise HTTPException(status_code=404, detail="Feature not available")

5. What is the rollout plan?
   Phase 1: Deploy with flag OFF → verify existing behaviour unchanged
   Phase 2: Enable flag for internal testing → verify new behaviour works
   Phase 3: Enable flag for all users → monitor feedback and error rates
   Rollback: Set flag to OFF → instant disable, no redeploy needed

6. When does the flag get removed?
   Flags are temporary. Add a reminder:
   # TODO: Remove feature flag after [date] once rollout is stable
   Permanent flags become unmanageable. Set a removal date before merging.

Show me the implementation plan. Do not write any code yet.
```

---

## FC Step 3.7 — AI Prompt Change Risk Gate

**Skip if: This change does not touch any AI prompt files.**
**Required for: Any change that modifies a prompt template, adds a new prompt,
changes model parameters, or changes how model output is parsed.**

**This step exists because AI prompt changes are categorically higher risk
than code changes.**

```
Code change:   deterministic — same input always produces same output
               regression is obvious — test either passes or fails
               blast radius is limited — one function, one endpoint

Prompt change: non-deterministic — output varies even with identical input
               regression is subtle — output seems fine but quality degraded
               blast radius is the entire AI layer simultaneously
               no type checker catches it, no linter catches it
               users notice before your tests do
```

**Mode: Ask — before any prompt change is written:**

```
This change modifies AI prompts. Before proceeding, answer:

1. SCOPE OF CHANGE
   Which exact prompt files are changing?
   Write the before and after for every template that changes.
   Be precise — show variable names, delimiters, and instruction text.

2. OUTPUT FORMAT RISK
   Does the new prompt produce output in the same format as before?
   Same structure: yes/no
   Same field names (if JSON output): yes/no
   Same response length range: yes/no
   
   If ANY of these is "no":
   - Which code currently parses this output?
   - Will that parsing code break on the new format?
   - What is the exact code change needed in the parser?

3. BEHAVIOUR CHANGE RISK
   What user-visible behaviour changes with this prompt?
   Is the change intentional? (yes — planned improvement)
   Or is the change a side effect? (unknown — needs eval)

4. EVAL COVERAGE
   How many test cases exist in tests/prompts/test_cases.json
   for the prompts being changed?
   Are there test cases covering the specific behaviour being changed?
   If fewer than 5 test cases exist: add more before changing the prompt.

5. ROLLBACK PLAN
   If the new prompt degrades quality in production:
   - What is the exact rollback? (revert commit + redeploy, or feature flag)
   - How quickly can rollback happen? (target: under 10 minutes)
   - How will you know the prompt is degrading? (feedback rate, eval score)

6. RISK RATING
   Rate this prompt change:
   LOW RISK:    Fixes a factual error, adds a clarification, tightens constraints
                Output format unchanged, behaviour change is narrow and intentional
   MEDIUM RISK: Changes tone, style, or reasoning approach
                Output format unchanged but response quality may vary
   HIGH RISK:   Changes output format, adds/removes output fields,
                changes model parameters (temperature, max_tokens),
                adds or removes tools/functions the model can call,
                changes the system prompt structure fundamentally

   If HIGH RISK: a feature flag is mandatory regardless of change size.
   If MEDIUM or HIGH: run the full eval suite before committing.
   If LOW: run the eval suite but a single PASS/FAIL is sufficient.
```

**Mandatory sequence for any prompt change — no exceptions:**

```
Step 1: Run evals on CURRENT prompt (establish baseline)
  python tests/prompts/run_evals.py
  Record: X/Y passed, average quality score Z/5

Step 2: Write the new prompt in a separate file
  Do NOT overwrite the current prompt yet
  backend/app/ai/prompts/main_prompt_candidate.py

Step 3: Run evals against the candidate prompt
  python tests/prompts/run_evals.py --prompt-file candidate
  Compare: did any PASS become FAIL? Did quality score drop?

Step 4: Decision gate
  Any regression (PASS→FAIL or quality drop ≥2): discard, redesign
  No regression AND improvement on target cases: proceed to Step 5
  No regression AND no improvement: question whether the change is needed

Step 5: Replace the current prompt with the candidate
  Overwrite main_prompt.py with the candidate content
  Bump PROMPT_VERSION
  Add change comment: # v[N] — what changed, why, eval result

Step 6: Run evals one final time on the updated file
  Confirm still no regressions after the file rename
```

**If this is a HIGH RISK prompt change:**

```
Also required before merging:

- Feature flag wrapping the new prompt
  When flag=OFF: use old prompt (keep old prompt file until flag is removed)
  When flag=ON: use new prompt

- Staged rollout plan:
  Day 1: Deploy with flag OFF. Confirm no impact.
  Day 2: Enable flag for 10% of requests. Monitor feedback rate.
  Day 3: Expand to 100% if feedback rate is stable.

- Monitoring in place:
  Alert if thumbs-down rate exceeds 20% in any 1-hour window
  Alert if eval quality score drops below baseline after enable
```

---

## FC Step 4 — Implement the Change

**Mode: Agent — one file at a time**

```
Execute the approved plan.

Strict rules during implementation:
- Change only the files listed in the approved plan
- If you find yourself wanting to change something outside the plan,
  STOP and tell me before doing it
- Match the existing code style in every file exactly:
  Same naming conventions, same error handling pattern,
  same logging format, same import order (stdlib / third-party / local)
  Do not introduce new patterns not already in this codebase
- After editing each file:
  Run: ruff check <filepath> — fix all errors before the next file
  Run: mypy <filepath> — fix all type errors before the next file
- Do not touch any test files in this step

After each file, tell me:
- Name of the file just changed
- A one-line summary of what changed
- Name of the next file you will change
- Running total: [N of M files complete]

If you encounter anything unexpected — a dependency that does not exist,
an interface that does not match, a pattern you did not expect —
STOP immediately and describe what you found before continuing.
```

**The diff size gate — pause and review at these checkpoints:**

```
Small change:   Review when ALL files are done (1 checkpoint)
Medium change:  Review after every 3 files (multiple checkpoints)
Large change:   Review after every 3 files (multiple checkpoints)
```

At each checkpoint, before continuing to the next batch:

```
Pause. We have completed [N] files.

Show me:
1. Every file changed so far and a one-line summary of each change
2. The total lines added and lines removed so far
3. Any unexpected discoveries made during implementation

If total lines changed exceeds 300 lines, stop completely.
We need to discuss splitting this into multiple smaller PRs
before continuing.
```

The 300-line threshold is a guide, not a hard rule. The point is that a diff
too large to review meaningfully in one sitting is a diff that should be split.

**Critical rule — scope creep:**

The moment Copilot says anything like "I also noticed X could be improved" or
"I took the liberty of cleaning up" — stop it immediately:

```
Stop. Revert any change not in the approved plan.
List every file you have changed so far in this session.
Confirm you will only change files in the approved plan.
```

---

## FC Step 4.5 — Performance Impact Check

**Skip if: Small change.**
**Required for: Medium and Large changes — always.**

Run this after implementation, before testing.

**Mode: Ask**

```
Read all files changed in this feature.

Check for performance impact — answer each question:

DATABASE QUERIES
1. Does any changed code add a new database query to a path
   that previously had no query?
2. Does any changed code add a query inside a loop?
   (This is an N+1 query — name the loop and the query)
3. Does any changed code load a large result set into memory
   when only a subset is needed?
4. Does the new DB migration add any indexes that should exist?
   Are there any new query patterns that need an index that is missing?

AI CALLS
5. Does any changed code add a new AI model call to an existing endpoint?
   If yes: what is the estimated latency added (tokens × model speed)?
6. Does any changed code increase the token count sent to the model?
   By how much and why?
7. Does any changed code add AI calls inside a loop or per-item processing?

ENDPOINT LATENCY
8. For every modified endpoint:
   What was the estimated response time before this change?
   What is the estimated response time after?
   Is the change acceptable given the SLA in the requirements?

MEMORY
9. Does any changed code buffer large amounts of data in memory
   for the duration of a request?
   Is there a streaming alternative?

For every performance issue found:
- Describe the issue concretely (not vaguely)
- Estimate the impact: milliseconds added, queries added, tokens added
- Propose the fix

Critical performance issues must be fixed before proceeding to testing.
Medium performance issues are noted and scheduled for a follow-up.
```

---

## FC Step 5 — Verify Existing Tests Pass

**Mode: Agent**

```
Run all existing tests for the modules that were changed.

Command: pytest tests/ -v --tb=short

For every test that now fails, tell me:
1. The exact test name and failure message
2. Why it is failing:
   - Is this a real regression? (our change broke something that worked)
   - Or does the test expectation need to update to match
     intentional new behaviour?

If it is a real regression:
   Fix the IMPLEMENTATION, not the test.
   The test was correct — our code change was wrong.

If the behaviour intentionally changed and the test is now outdated:
   Update the test and add this comment above it:
   # Updated: [date] — behaviour changed because [reason]

Rules:
- Do not mark any test as skip or xfail to make the count green
- Do not delete a test to make the count green
- Do not change assertion values without understanding why they changed

Report when done:
X tests passing, 0 failing.
```

**Zero regressions is non-negotiable.**
If you cannot reach zero, go back to FC Step 4 and fix the implementation.
Do not paper over broken tests and move forward.

---

## FC Step 6 — Write New Tests

**Mode: Agent**

```
Read the changes made in FC Step 4.

Generate new tests that cover the new feature.

Use the existing test structure and conventions — match exactly:
- Same folder layout as existing tests (tests/unit/ or tests/integration/)
- Same file naming pattern as the rest of the test suite
- Same fixture patterns already used in conftest.py
- Same mock approach for AI APIs and external services

Write these test categories:

1. Happy path tests
   - Every new code path added by this feature
   - Exact inputs and the expected outputs

2. Failure path tests
   - Every new error condition introduced
   - What happens when each new dependency fails
   - What the API returns when validation fails

3. Backward compatibility tests (if API contracts changed):
   - Test that the OLD request format still returns a valid response
   - Test that the OLD response fields are still present in the new response
   - Name these: test_backward_compat_<what_is_preserved>

4. Performance regression tests (if performance-sensitive paths changed):
   - Use pytest-benchmark or a simple timing assertion
   - Assert the endpoint completes within the expected SLA
   - Name these: test_perf_<endpoint_name>_within_sla

5. Feature flag tests (if a feature flag was added):
   - Test behaviour when flag is OFF (default): feature not accessible
   - Test behaviour when flag is ON: feature works correctly
   - Test that flag=OFF does not break any existing behaviour

6. Edge cases specific to this feature:
   [describe your specific edge cases here]

   Standard edge cases to always include:
   - None input where the feature accepts optional data
   - Empty string where string input is accepted
   - Maximum allowed value for any numeric input
   - Concurrent calls if the feature modifies shared state

7. AI-specific tests (only if this feature touches the AI layer):
   - AI API returns an unexpected response format
   - AI API is completely unavailable
   - Token limit is exceeded before the call is made
   - Prompt injection attempt is blocked or sanitised

8. One regression test that locks this feature permanently:
   Name it: test_<feature_name>_regression
   It must assert the complete end-to-end behaviour of this feature.
   Any future change that silently breaks this feature must fail this test.

Run all tests — old and new together:
pytest tests/ -v --cov=backend --cov-report=term-missing

Report: X passing, 0 failing, coverage %.
```

---

## FC Step 7 — Review the Diff

**Mode: Plan first, then Agent**

This is the dual-model review applied only to the changed files.

**Quick version for Small changes:**

```
Review the files changed in this feature: [list them]

Check for: correctness, security issues, missing error handling.
Flag Critical and High issues only.
Do not comment on style or medium/low concerns.
```

**Full version for Medium and Large changes:**

```
Review only the files that were changed in this feature.

Files changed: [list the files from FC Step 4]

For each changed file, check:

CORRECTNESS
- Does the logic match what was planned and approved?
- Any off-by-one errors or wrong conditions?
- Async/await used correctly throughout?

PYTHON PATTERNS
- Does it match the existing code style in this file?
- Are type annotations correct and complete on all public functions?
- Are Pydantic models used correctly at data boundaries?

ERROR HANDLING
- Is error handling consistent with the rest of this module?
- Are the same named exception classes used as the rest of the project?
- Are errors logged in the same format as the rest of the project?

SECURITY
- Any new user input that is not validated?
- Any new data in API responses that should not be exposed?
- If AI prompts changed: is user input sanitised before injection?
- Any new endpoints missing authentication?

BACKWARD COMPATIBILITY
- Does the old request format still work?
- Are all old response fields still present?
- Would existing callers break silently?
- Is the DB migration safe to run on live data?

PERFORMANCE
- Any N+1 queries introduced?
- Any new AI calls on a hot path?
- Any synchronous I/O blocking an async event loop?
- Any large data loaded into memory that should be streamed?

FEATURE FLAG (if applicable)
- Is the default value OFF?
- Is every new code path guarded by the flag?
- Is old behaviour fully preserved when flag is OFF?

AI LAYER (only if this feature touches AI logic)
- Is the new prompt template parameterised? No hardcoded values?
- Is the timeout and retry still in place on the AI call?
- Is cost tracking still working for new AI calls?
- Does output validation still apply to new response paths?
- Was PROMPT_VERSION bumped if any prompt template changed?

TESTS
- Do the new tests actually test the new behaviour?
- Is there any new code path with no corresponding test?
- Are backward compat tests present if API changed?

Risk level per issue: Critical / High / Medium / Low

Review changed files only. Do not comment on unrelated code.
Do not fix anything yet. Show the full report and wait for my instruction.
```

Then switch to Agent mode:

```
Fix all Critical and High issues from the review.
Do not touch anything outside the files already changed in this feature.
After every fix: run pytest tests/ -v and confirm tests still pass.
Show a diff of every change made.
```

---

## FC Step 8 — Pre-Commit Safety Check and Commit

**Mode: Ask**

This step has two parts. The pre-commit safety check runs first against the
actual git diff. The Copilot final summary runs second. Both must pass
before you commit.

### Part A — Pre-commit safety check (you run this manually)

Before asking Copilot anything, run these commands yourself and read the output:

```bash
# 1. See exactly what changed — every file, every line
git diff --stat                    # file-level summary
git diff                           # full line-level diff

# 2. Confirm only planned files changed
git diff --name-only

# 3. Count total lines changed
git diff --shortstat

# 4. Check for anything that should never be committed
git diff | grep -E "print\(|debugger|TODO:|FIXME:|password|api_key|secret"
# If any of these appear — stop. Fix before continuing.

# 5. Check no test files were deleted
git diff --name-only --diff-filter=D | grep test
# If any test files show as deleted — stop. Restore them.
```

**Hard stops — do not commit if any of these are true:**

```
Any file changed that was not in the approved plan   → investigate, revert if needed
Total diff exceeds 300 lines                          → split into multiple PRs
Any secret, password, or API key in the diff          → remove immediately, rotate key
Any test file deleted                                 → restore it
Any print() or debug statement in production code     → remove it
Lint or type check fails                              → fix before committing
```

Only when the git diff looks exactly as expected — proceed to Part B.

### Part B — Copilot final summary

```
Give me a final summary before I commit:

1. Every file that was changed — list them
2. Every new file that was created — list them
3. Every new test that was added — list them
4. All tests passing — confirm with exact count
5. No tests were deleted or skipped — confirm
6. Scope stayed within the original approved plan — confirm
   If anything was changed outside the plan, list it explicitly
7. This PR is atomic — one logical change, one rollback boundary — confirm
8. Was a DB migration created?
   If yes: was migration tested with upgrade AND downgrade on staging?
   Is alembic upgrade head part of the deploy process?
9. Did any AI prompts change?
   If yes: was PROMPT_VERSION bumped and a change comment added?
   If yes: did the eval suite pass with no regressions?
   If HIGH RISK: is a feature flag in place?
10. Backward compatibility confirmed:
    If any API contracts changed: are old callers still supported?
    If DB migration: is it SAFE or UNSAFE? Expand-contract followed if UNSAFE?
11. Performance impact confirmed:
    No new N+1 queries, no unacceptable latency added?
12. Feature flag in place (if Large change or HIGH RISK prompt change):
    Default is OFF, old behaviour preserved when OFF?
13. Code rollback plan — state the exact sequence:
    If feature flag exists: set FEATURE_<n>=false, restart → instant disable
    If no feature flag: git revert <commit>, push, redeploy
    If DB migration: alembic downgrade -1 BEFORE reverting code
14. Anything I should manually verify in the running app
    before merging this branch?
```

Read the summary. Investigate anything unexpected before committing.

**You commit manually — Copilot never commits:**

```bash
git add .
git commit -m "feat(<module>): <what changed and why>

- <specific change 1>
- <specific change 2>
- atomic: [one sentence describing the single logical change]
- migration: <schema change description if applicable>
- prompt: bumped to v[N] — <reason if AI prompt changed>
- flag: FEATURE_<n> defaults to OFF — enable to activate

Closes #<issue number if applicable>"

git push origin feature/<your-feature-name>
```

---

## FC Step 8.5 — Staging Verification

**Skip if: Small change with no new endpoints, no schema changes, no AI changes.**
**Required for: Medium and Large changes — before merging to main.**

After pushing, deploy to staging and verify the feature works in a live
environment before the branch merges to main. Tests pass in CI but staging
catches integration issues, environment variable differences, and migration
behaviour on real data.

**You do this manually — no Copilot involved.**

```bash
# 1. Deploy to staging
# Railway:  git push staging feature/<n>:main
# Render:   trigger manual deploy from staging branch
# Fly.io:   fly deploy --app your-app-staging

# 2. Confirm the app started correctly
curl https://your-staging-app.com/health
# Expected: {"status": "healthy", "database": "connected", "ai_api": "reachable"}

# 3. If a DB migration ran — verify it on staging DB
# Confirm schema change applied, existing data not corrupted, row counts correct
```

**Manual smoke tests — run every applicable one:**

```
Existing features (confirm nothing broke):
  ✓ Log in with an existing account
  ✓ Perform the most common user action
  ✓ Most-used API endpoint returns valid response

New feature:
  ✓ Trigger with valid input — confirm correct response
  ✓ Trigger with invalid input — confirm correct error, not a 500

If DB migration ran:
  ✓ New record can be created with new schema
  ✓ Existing record can still be read correctly

If AI prompt changed:
  ✓ Send 3 real inputs manually and read responses
  ✓ Output format matches what the parser expects
  ✓ Response quality is acceptable on real inputs

If feature flag was added:
  ✓ With flag=OFF: old behaviour works, new feature inaccessible
  ✓ Enable flag → confirm new feature works → disable flag again
```

**Result:**

```
PASS: All checks pass, no unexpected errors in staging logs
      → Merge to main, deploy to production

FAIL: Any check fails or unexpected errors appear
      → Do NOT merge. Fix the issue. Re-run staging verification.
      → If migration failed: alembic downgrade -1 on staging before reverting code
```

---

## FC If Agent Goes Off-Scope

This will happen at some point. The moment you see Copilot editing a file
not in the approved plan:

```
Stop immediately.
Do not make any more changes.

List every file you have changed in this session,
including any that were not in the approved plan.
```

Then decide:
- If the out-of-scope change is harmless: keep it, document it in the commit message
- If it is not harmless: revert the entire branch and start FC Step 4 again

```bash
# Revert everything and start FC Step 4 again with a stricter prompt
git checkout .

# Or revert a single file only
git checkout -- path/to/file.py
```

Restart FC Step 4 with this line added to the implementation prompt:

```
Strict file list — you may ONLY edit these files.
If you are about to edit anything not on this list, STOP and ask me first:
[list every file in the approved plan]
```

---

## FC Quick Reference

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CHANGE SIZE — CLASSIFY FIRST
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Small    1-3 files · no new endpoints · no schema change
Medium   4-10 files · may add 1 endpoint · additive API change
Large    10+ files · OR multiple endpoints · OR breaking contract
         OR DB migration on existing data · OR new AI call

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
STEP ORDER BY SIZE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Before Copilot    You          git checkout -b · pytest green
FC Step 0         You          Baseline check (all sizes)
FC Step 1         Ask          Map codebase (Medium + Large)
FC Step 2         Ask          Impact analysis (all sizes, quick or full)
FC Step 3         Plan         Propose plan · atomic check · repeat back · approve
FC Step 3.5       Ask          Backward compat + migration safety (Medium + Large)
FC Step 3.6       Plan         Feature flag strategy (Large only)
FC Step 3.7       Ask+Plan     AI prompt risk gate (all sizes — if AI touched)
FC Step 4         Agent        Implement · diff gate every 3 files
FC Step 4.5       Ask          Performance impact check (Medium + Large)
FC Step 5         Agent        pytest green · zero regressions (all sizes)
FC Step 6         Agent        New tests + compat + perf + flag tests (all sizes)
FC Step 7         Plan+Agent   Review diff · quick or full (all sizes)
FC Step 8         You+Ask      Pre-commit git diff check · then Copilot summary
FC Step 8.5       You          Staging verification (Medium + Large)
After staging     You          Merge to main · deploy to production

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DIFF SIZE GATE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Small:   Review when all files done
Medium:  Pause and review after every 3 files
Large:   Pause and review after every 3 files
>300 lines changed total → split into multiple PRs before continuing

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ATOMIC CHANGE RULE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
One PR = one logical change = one sentence description = one rollback unit
Test: can you describe this PR in a single sentence?
      If no → split it into separate PRs before approving the plan

Non-atomic examples that must be split:
  Fix bug AND add feature       → two PRs
  Add table AND migrate data    → two PRs (expand, then contract)
  Refactor AND change behaviour → two PRs (refactor first, then feature)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PRE-COMMIT SAFETY CHECKS (you run these manually)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
git diff --name-only              → only planned files changed?
git diff --shortstat              → under 300 lines?
git diff | grep -E "secret|api_key|password|print\\(" → clean?
git diff --name-only --diff-filter=D | grep test    → no tests deleted?

Hard stops — do not commit if any of these are true:
  Unplanned file changed          → investigate and revert
  Over 300 lines total            → split the PR
  Secret or key in diff           → remove it, rotate the key
  Test file deleted               → restore it
  print() or debug in prod code   → remove it

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BACKWARD COMPATIBILITY RULES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Safe:    Add new optional fields to requests/responses
Safe:    Add nullable columns to DB
Safe:    Add new endpoints (does not touch existing ones)
Unsafe:  Remove or rename existing request/response fields
Unsafe:  Add NOT NULL columns without default to existing table
Unsafe:  Change existing field types
Unsafe:  Remove or rename existing endpoints
Breaking changes require versioned endpoint or deprecation period

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DATA MIGRATION SAFETY RULES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SAFE migrations (run directly):
  ADD COLUMN nullable=True       → existing rows get NULL
  ADD TABLE                      → no existing data affected
  ADD INDEX CONCURRENTLY         → Postgres only, no table lock
  DROP INDEX                     → no data affected

UNSAFE migrations (use expand-contract across 3 separate PRs):
  ADD COLUMN nullable=False      → existing rows fail
  RENAME COLUMN                  → existing queries break
  CHANGE COLUMN TYPE             → existing data may not convert
  DROP COLUMN / DROP TABLE       → data loss if done prematurely

Always before running on production:
  Test upgrade AND downgrade on staging first
  alembic upgrade head → alembic downgrade -1 → alembic upgrade head
  If downgrade fails on staging → fix migration before touching production

Rollback order when DB migration involved:
  1. alembic downgrade -1 FIRST
  2. Revert code SECOND
  Never revert code before reverting the DB

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
AI PROMPT CHANGE RISK LEVELS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LOW RISK     Fixes factual error · adds clarification · tightens constraints
             Output format unchanged · narrow intentional behaviour change
             Action: run eval suite, PASS/FAIL sufficient

MEDIUM RISK  Changes tone, style, or reasoning approach
             Output format unchanged but quality may vary
             Action: run full eval suite, no quality score drop ≥2

HIGH RISK    Changes output format or field names
             Changes model parameters (temperature, max_tokens)
             Adds/removes tools the model can call
             Changes system prompt structure fundamentally
             Action: feature flag MANDATORY · staged rollout · monitoring alert

Mandatory sequence for ALL prompt changes (no exceptions):
  1. Run evals on current prompt → record baseline score
  2. Write candidate in separate file (do not overwrite yet)
  3. Run evals against candidate → no regressions allowed
  4. Replace current prompt only after candidate passes
  5. Bump PROMPT_VERSION · add change comment
  6. Run evals one final time to confirm

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
STAGING VERIFICATION (Medium + Large — before merging)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Deploy to staging → GET /health must return healthy
Existing feature smoke tests → confirm nothing broke
New feature smoke test → valid input works, invalid input errors correctly
If migration ran → schema change applied, existing data intact
If AI prompt changed → 3 real inputs, format correct, quality acceptable
If feature flag → test OFF behaviour, enable, test ON behaviour, disable
PASS → merge to main · deploy to production
FAIL → do not merge · fix issue · re-run staging verification

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FEATURE FLAG RULES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Default: OFF — deploy without activating
Enable:  Change env var, restart app (no redeploy needed)
Rollback: Set flag to OFF — instant disable, zero code change
Cleanup: Set removal date before merging. Flags are temporary.
Required: Large changes · HIGH RISK prompt changes · phased rollout

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CODE ROLLBACK PLAN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
With flag:    Set FEATURE_<n>=false → restart → instant disable
Without flag: git revert <merge-commit-hash>
              git push origin main → trigger redeploy
DB migration: alembic downgrade -1 BEFORE reverting code (always)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BASELINE SCENARIOS — FC STEP 0
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
pytest passes          → Clean baseline. Continue.
"collected 0 items"    → Scenario A: characterisation tests first.
pytest not installed   → Scenario B: install pytest, then Scenario A.
Tests failing          → Fix baseline first. Never start on broken ground.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RULES THAT NEVER CHANGE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Atomic PR          One logical change · one sentence · one rollback unit
Zero regressions   Never skip, xfail, or delete a test to make count green
                   Fix the implementation — not the test
Scope discipline   Stop Copilot the moment it touches an unplanned file
Plan first         Never implement without an approved and repeated-back plan
One file at a time ruff + mypy after every file before moving to next
Git diff first     Run git diff --name-only before Copilot final summary
Staging before     Deploy to staging and verify before merging to main
You commit         Copilot never commits — you review the diff and commit

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
AI LAYER CHANGES — EXTRA CHECKS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Prompt changed?      Run FC Step 3.7 first — no exceptions
                     LOW/MEDIUM: full eval suite, no quality regression
                     HIGH: feature flag mandatory + staged rollout + alert
                     Always: candidate file → eval → replace → bump version
New AI endpoint?     Add to openapi.yaml · confirm cost tracking
                     Confirm timeout and retry still in place
New user input?      Sanitise before prompt injection
                     Add prompt injection test in FC Step 6
New model call?      Confirm timeout (30s) · backoff retry · fallback

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
COMMIT MESSAGE FORMAT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
feat(<module>): <what changed and why>

- atomic: [one sentence — the single logical change this PR makes]
- <specific change 1>
- <specific change 2>
- migration: <schema change if applicable>
- prompt: bumped to v[N] — <reason> — risk: LOW/MEDIUM/HIGH
- flag: FEATURE_<n> defaults to OFF

Closes #<issue number>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
---

*Python + FastAPI + React/TypeScript + OpenAI/Anthropic/LangChain*
*MVP in days. Production when it matters.*
