# Senior Developer Feature Change Workflow
### Repo Analysis → Feature Planning → Implementation → Ship
### Built on GitHub Copilot · Covers every size of change

---

## How This Workflow Works

Two documents you already have become one connected pipeline:

```
REPO ANALYZER          →    FEATURE CHANGE WORKFLOW
(understand the system)      (safely change the system)

Analysis outputs feed        FC Steps consume the
directly into FC planning.   analysis — nothing re-done.
```

Every change follows the same spine. What changes per size is depth, not discipline.

---

## Copilot Mode Reference

```
Ask mode    → Reads and explains. Touches ZERO files.
              Use for: understanding, analysis, debugging, impact assessment

Plan mode   → Proposes. You approve before ANYTHING is written.
              Use for: planning, architecture decisions, review reports

Agent mode  → Executes. Creates and edits files.
              Use for: implementation, test writing, running commands
```

**Rule:** Never jump to Agent mode without a Plan mode approval first.  
Switch modes: `Ctrl+Shift+I` → dropdown at top of Copilot Chat panel.

---

## Change Size — Classify Before Opening Copilot

Classify your change first. This determines which steps are required.

```
SMALL    1–3 files · no new endpoints · no schema changes · no AI changes
         Example: fix a bug, update validation, rename a field

MEDIUM   4–10 files · may add 1 endpoint · additive API change
         Example: add a new feature to existing flow, add a new service method

LARGE    10+ files · multiple endpoints · breaking contract change
         OR: DB migration on existing data · new AI call or prompt change
         Example: new module, new user-facing feature, auth redesign
```

If you cannot classify, default to MEDIUM. Never default down — only up.

---

## Hotfix Mode — Emergency Production Fixes Only

Use this mode when production is broken and a full workflow is not viable. This is an exception, not a shortcut.

```
HOTFIX TRIGGER CONDITIONS
A hotfix is justified only if ALL of these are true:
  ✓ Production is actively broken or data is at risk
  ✓ The fix is 1–3 lines and the cause is known with certainty
  ✓ You can describe the fix in one sentence
  ✓ The change can be tested locally in under 10 minutes

HOTFIX STEPS (abbreviated)
1. Branch from main:  git checkout -b hotfix/<description>
2. Make the minimal fix only — nothing else
3. Run existing tests:  pytest tests/ -v --tb=short
4. Manual smoke test on staging — do not skip
5. Dual-model review (Claude or ChatGPT) — even for 1 line
6. Commit with prefix:  fix(<module>): HOTFIX — <description>
7. Merge to main, deploy immediately
8. Monitor for 30 minutes
9. Open a follow-up PR within 24 hours for the proper solution

HOTFIX HARD LIMITS
  ✗ More than 3 files changed → this is not a hotfix, run the full workflow
  ✗ Schema migration required → this is not a hotfix, run the full workflow
  ✗ Root cause unclear → do not ship, investigate first
  ✗ No tests pass → do not ship until they do

Follow-up PR must:
  - Add tests that would have caught this bug
  - Add logging/observability so this class of failure is visible in future
  - Go through the full workflow with no shortcuts
```

---

## Master Step Map

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 0 — REPO ANALYSIS (before anything else)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RA-0   Setup             You          One-time: instructions file + snippet + output template
RA-1   Trigger           You          Checklist before running analysis
RA-2   Full Analysis     Ask/Agent    Run analyzerepo · let Copilot explore
RA-3   Depth Passes      Ask          Domain deep-dives · targeted refinement
RA-4   Save Output       Agent        Populate docs/ARCHITECTURE_ANALYSIS.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 1 — PRE-CHANGE PREPARATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FC-0   Baseline          You          git branch · pytest green · classify size
FC-0.5 Change Ownership  You          Assign owner · reviewer · rollback owner
FC-1   Codebase Map      Ask          Full map (Medium + Large) · skip if small + familiar
FC-1.5 Requirement       Ask          Validate requirement before planning (Medium + Large)
       Validation
FC-2   Impact Analysis   Ask          What changes, what breaks, what risks (all sizes)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 2 — PLANNING & APPROVAL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FC-3   Plan + Approve    Plan         Atomic check · file order · repeat-back · approve
FC-3.5 Compat Check      Ask          API + DB + AI compat (Medium + Large)
FC-3.6 Feature Flag       Plan         Flag strategy (Large + AI behaviour changes)
FC-3.7 AI Risk Gate       Ask          Prompt risk level · eval baseline (if AI touched)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 3 — IMPLEMENTATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FC-4   Implement         Agent        One file at a time · diff gate every 3 files
FC-4.5 Perf Check        Ask          Query / AI / latency / memory (Medium + Large)
FC-5   Existing Tests    Agent        pytest green · zero regressions · fix impl not tests

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 4 — QUALITY & SHIP
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FC-6   New Tests         Agent        Happy/fail/compat/perf/flag/AI tests
FC-7   Diff Review       Plan+Agent   Dual-model review · fix Critical + High
FC-8   Pre-Commit        You + Ask    git diff check · Copilot summary · you commit
FC-8.5 Rollback Test     You          Simulate rollback on staging (Medium + Large)
FC-8.7 Staging           You          Deploy + smoke tests (Medium + Large)
Ship   Merge & Deploy    You          Merge to main · deploy
FC-9   Post-Deploy       You          Active verification · not just monitoring
```

---

# PHASE 0 — REPO ANALYSIS

Run this when:
- You are about to make a change to an unfamiliar codebase
- You are returning to a codebase after a long gap
- A new team member needs to get up to speed
- You need to scope a feature before estimating it

Skip this if you wrote this code recently and know it well. Never skip for an unfamiliar system.

---

## RA-0 — One-Time Setup

Do this once per repository. Never again unless you change machines.

**Step 1: Place the system instruction file**

Create `.github/copilot-instructions.md` in the repo root. Copilot Agent mode reads this automatically. The full content of this file is in the companion document `copilot-instructions_Repo_analyzer.md`.

```
.github/
  copilot-instructions.md   ← paste the full repo analyzer instructions here
docs/
  ARCHITECTURE_ANALYSIS.md  ← create this empty file (Copilot writes to it)
```

**Step 2: Create the VS Code snippet**

Open VS Code → `Ctrl+Shift+P` → `Snippets: Configure User Snippets` → `markdown.json`

Add this entry:

```json
{
  "Analyze Repo": {
    "prefix": "analyzerepo",
    "body": [
      "Perform a complete deep analysis of this repository following your system instructions strictly.",
      "",
      "If this repository is large, ask me to define a focus area before proceeding.",
      "",
      "Start with an exploration declaration: files to read, why each one, expected outcome from each.",
      "",
      "Deliver output covering all 27 sections:",
      "Top 3 Critical Risks | Exploration Log | Assumptions Registry |",
      "1. Overview | 2. System Boundaries | 3. Contracts & Interfaces |",
      "4. Folder Structure | 5. Entry Points | 6. Runtime Behaviour | 7. Configuration Risk |",
      "8. Deployment Risk | 9. Execution Flow | 10. Control Flow Ownership |",
      "11. Data Ownership & Flow Authority | 12. Concurrency & Parallelism |",
      "13. Core Modules | 14. Dependency Analysis | 15. Design Patterns |",
      "16. Anti-Pattern Detection | 17. Test Strategy | 18. Security Posture |",
      "19. Performance Signals | 20. Observability | 21. Failure Path Analysis |",
      "22. Scalability Direction | 23. Change Impact Analysis | 24. Issues and Risks |",
      "25. Prioritised Improvements | 26. Reading Priority Guide | 27. Developer Onboarding Guide",
      "",
      "End with a structured 'Not covered in this pass' table: Area | Reason Skipped | Risk.",
      "Exploration Log must include Confidence per file. Confidence on every section header.",
      "Declare all assumptions. Ask for missing files before guessing.",
      "Top 3 Critical Risks: written last, placed first in output."
    ]
  }
}
```

---

## RA-1 — Trigger Checklist

Run through this before typing `analyzerepo`. Context quality depends on it.

- [ ] Switch Copilot Chat to **Agent mode**
- [ ] Add `#codebase` as context in the chat input
- [ ] Open `README.md` in the editor tab
- [ ] Open the main entry file (`main.py`, `index.js`, `app.go`, etc.)
- [ ] Open the package manifest (`package.json`, `requirements.txt`, `go.mod`)
- [ ] Open one config file (`.env.example`, `config/settings.py`, etc.)
- [ ] For large repos: decide the focus area now and state it in the first line of your prompt

---

## RA-2 — Run the Full Analysis

Type `analyzerepo` in Copilot Chat. The snippet expands.

**What to do while Copilot runs:**

When Copilot says "I'd need to see [filename] to answer this accurately" — open that file in the editor and say:

```
I've opened [filename]. Continue from where you stopped.
```

This is the most important habit. Do not skip it. Do not demand the answer without the file.

**What the analysis produces:**

```
Top 3 Critical Risks        ← read this first, share with team
Exploration Log             ← what was read, confidence per file, gaps
Assumptions Registry        ← what Copilot assumed, what to verify
Sections 1–27               ← full architectural picture
Not Covered table           ← what to tackle in the next pass
```

**Confidence levels on every section:**

```
High    → verified from files read directly — trust this
Medium  → inferred from adjacent files — verify before acting
Low     → based on conventions only — verify before acting
```

Never act on a Medium or Low confidence finding without opening the relevant file and confirming.

---

## RA-3 — Targeted Depth Passes

After the full analysis, run these targeted prompts for areas relevant to your feature:

**Before any feature change — always run this:**

```
Based on your analysis, I need to change [describe the feature area].

Which modules are in the blast radius of this change?
Which existing tests cover those modules?
What is the riskiest part of touching this area?
What assumptions does the current code make that my change might violate?
```

**If touching execution flow:**

```
Trace the execution flow for [the specific operation my feature affects], file by file.
Include data flow (request → processing → storage → response) and any failure paths.
```

**If touching data:**

```
Map data ownership for [entity name]. Who writes it, who reads it, where is the
source of truth, and are there any duplicated copies that could diverge?
```

**If touching auth or security:**

```
Explain the authentication and authorisation system — how identity is established,
how permissions are enforced, and what the trust boundaries are.
What gaps exist that my change could accidentally widen?
```

**If touching an API contract:**

```
Map the contract for [endpoint or interface]. What does the current request look like?
What does the response look like? What callers depend on it?
What would break silently if I changed it?
```

---

## RA-4 — Save the Analysis

Once the analysis is complete and you have run your targeted depth passes:

```
Analyse this repository and populate docs/ARCHITECTURE_ANALYSIS.md completely
using everything you have learned in this session. Use the full 27-section structure.
```

This file becomes your reference throughout the feature change. Keep it open.

---

# PHASE 1 — PRE-CHANGE PREPARATION

---

## FC-0 — Baseline Check

**You do this manually. No Copilot.**

```bash
# 1. Create a feature branch — never work on main
git checkout -b feature/<your-feature-name>

# 2. Confirm tests are green before touching anything
pytest tests/ -v --tb=short

# 3. Classify your change size (see top of document)
```

**If pytest fails before you touch anything:**

Fix the failing tests before starting. Do not continue until `pytest` is green. Never start implementation on broken ground.

**If pytest reports "collected 0 items":**

No tests exist. Before implementing, generate characterisation tests:

```
Read the backend/ folder. For each public function and API endpoint that exists,
write a characterisation test that documents the current behaviour.
These tests must pass before I make any changes.
Place them in tests/ matching the existing folder structure.
```

---

## FC-0.5 — Change Ownership Assignment

**You do this before opening Copilot. All change sizes.**

Ownership must be clear before any planning begins. If something breaks after deployment, there should be no ambiguity about who acts.

```
CHANGE OWNERSHIP RECORD
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature name:       [name]
Feature owner:      [name] — responsible for the change, reviews all decisions
Reviewer:           [name] — reviews the diff and approves the PR
Rollback owner:     [name] — responsible for executing rollback if production breaks
                             (can be the same as feature owner for solo developers)

Estimated effort:   Small / Medium / Large
Risk level:         Low / Medium / High
Areas of uncertainty: [list anything you don't fully understand yet]

Merge window:       [time and day — avoid Friday afternoons and major traffic peaks]
On-call coverage:   [who is available to respond if something breaks post-deploy]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

The risk level and areas of uncertainty feed directly into how cautious the planning phase should be. If risk is High or uncertainty is significant, default to LARGE change classification regardless of file count.

---

## FC-1 — Codebase Map

**Required for: Medium and Large changes, or any Small change in unfamiliar code.**  
**Skip if: Small change and you know this codebase well (and ran the repo analyzer recently).**

If you completed RA-2 and RA-3 recently, reference `docs/ARCHITECTURE_ANALYSIS.md` instead. Do not re-run what the analyzer already produced.

If the analysis is stale or you need a focused map for your specific area:

**Mode: Ask**

```
Read the [backend/ or src/] folder.

Give me a focused codebase map for the area I'm about to change: [describe your feature area].

1. Every module in this area and what it does (one sentence each)

2. Naming conventions used consistently:
   - File names, function names, class names, variable names

3. Error handling pattern in this area:
   - Named exception classes or generic exceptions?
   - Where are exceptions caught vs propagated?
   - What is the error response format?

4. Logging approach:
   - Which logger, what format, what fields on every request?

5. How external dependencies are accessed:
   - Database: ORM, raw SQL, or repository pattern?
   - AI APIs: which client, where is the client created?

6. Test structure for this area:
   - Where do tests live? What fixtures exist? What is already mocked?

Do NOT suggest any changes. Map only.
```

Read the output carefully. Correct anything Copilot got wrong before proceeding.

---

## FC-1.5 — Requirement Validation

**Skip if: Small change where the requirement is a direct bug fix with a clear root cause.**  
**Required for: Medium and Large changes — always.**

This step exists because implementation is the most expensive place to discover you are building the wrong thing. Validate the requirement before planning anything.

**Mode: Ask**

```
Before I plan the implementation, validate this requirement with me.

The feature I want to build:
[describe it in plain language]

Answer these questions without touching any files:

PROBLEM CLARITY
1. What specific user problem does this solve?
   Who are the users affected and what is their current experience?
2. What is the measurable success metric?
   How will we know this feature achieved its goal? Be specific.
3. What is explicitly OUT OF SCOPE for this change?
   What might users expect that we are intentionally not delivering?

DUPLICATION CHECK
4. Is any part of this already implemented somewhere in the codebase?
   Look for existing endpoints, services, or utilities that partially handle this.
   If yes: should we extend what exists or add alongside it? What is the risk of each?
5. Does this duplicate functionality that exists in a different form?
   Example: adding a new filtering endpoint when a more general one already exists.

SYSTEM FIT CHECK
6. Is this consistent with the system boundaries identified in the repo analysis?
   Does it introduce a new external dependency or change the system's responsibility?
7. Does this conflict with any architectural decisions or constraints in the codebase?
   Does it require a pattern that is not used anywhere else?

AMBIGUITY FLAGS
8. What parts of this requirement are ambiguous or underspecified?
   List every open question that, if answered differently, would change the implementation.
9. Are there any edge cases in the requirement that have not been defined?
   What happens with empty state, first-time users, high volume, or errors?

If any answer reveals that the requirement is unclear, incomplete, or conflicts
with existing system design — flag it explicitly.
Do NOT proceed to planning until these are resolved.
```

**Stop condition:** If Copilot identifies ambiguities or conflicts that you cannot resolve, stop here and clarify the requirement with the stakeholder or team. Do not proceed to FC-2 with an unresolved requirement. Building the wrong thing correctly is still the wrong thing.

---

## FC-2 — Impact Analysis

**Mode: Ask — all change sizes, quick or full version.**

**Quick version for Small changes:**

```
I want to make this change: [describe it precisely — not vaguely]

Using your knowledge of this codebase:
1. Which files will need to change and why?
2. Does this touch any public API contracts?
3. Does this require a DB migration?
4. What is the riskiest part of this change?
5. What does the repo analysis say about the blast radius of this area?

Analysis only. No solutions yet.
```

**Full version for Medium and Large changes:**

```
I want to make this feature change:

[describe your feature in specific, concrete terms]
Example of too vague:   "add user authentication"
Example of specific:    "add JWT-based login endpoint that accepts email and password,
                         validates against the users table, returns a 24-hour access token"

Using your knowledge of this codebase from the repo analysis, answer without touching any files:

1. Which existing files will need to change?
   List every file and the exact reason it needs to change.

2. Which files are affected indirectly?
   Files that import from or call the files above.

3. Which existing tests cover the code being changed?
   List each test file and what it currently asserts.

4. Does this change require a database migration?
   If yes: what table, what columns, what type, nullable or not?

5. Does this change add, remove, or modify any API endpoints?
   If yes: what changes to openapi.yaml are needed?

6. Does this change affect any AI prompts or AI layer behaviour?
   If yes: which prompt files change and what exactly changes?
   Estimated cost impact: (new tokens per request × requests per day × price per token)
   Any risk of unexpected cost spikes under high traffic?

7. Does this change affect any PUBLIC API contracts that existing callers depend on?
   - Are any existing request fields being removed or renamed?
   - Are any existing response fields being removed or renamed?
   - Are any previously optional fields becoming required?

8. What does the repo analysis say about data ownership in the affected area?
   Could my change create a write conflict or duplicate a source of truth?

9. What does the repo analysis say about control flow in the affected area?
   Are there hidden control paths (callbacks, middleware) my change could affect?

10. What is the riskiest part of this change?
    Where is the most likely place something silently breaks?

11. What is the estimated performance impact?
    - New database queries on a hot path?
    - New AI calls or increased token usage?
    - Added latency to existing endpoints?

12. Is there any tech debt in the affected area that makes this harder than it should be?

Analysis only. No solutions yet.
```

Read this carefully. A feature that looks like one file often touches five.

---

# PHASE 2 — PLANNING & APPROVAL

---

## FC-3 — Propose and Approve the Plan

**Mode: Plan**

```
Based on your impact analysis, propose the exact change plan.

ATOMIC CHANGE RULE — enforce before writing the plan:
This change must represent exactly one logical concern.
One logical change = one thing describable in a single sentence,
deployable independently, rollback-able independently.

Non-atomic examples that must be split:
  "add new endpoint AND fix existing validation bug"   → two PRs
  "add preference table AND migrate existing users"    → two PRs
  "refactor auth module AND add new auth feature"      → two PRs (refactor first)

Is this change atomic? If not, list the separate concerns. I will decide how to split them.

If atomic, propose:
- For each file that needs to change:
  * What currently exists
  * What you will add, change, or remove
  * Why that change is needed

- The exact order you will make these changes
  (dependencies first — if File B imports File A, change File A first)

- Total files to change and estimated lines changed

- DB migration needed?
  Show the exact Alembic command and expected migration content

- AI prompt changes?
  Show before and after for every template that changes
  Confirm PROMPT_VERSION will be bumped
  Estimated cost impact per request and per day at expected traffic

- API contract changes?
  Show before and after of every request/response schema

- Observability additions for this feature:
  What logging will be added at key decision points?
  What error logging with context will be added?
  What metrics will be emitted (if performance-critical path)?
  What correlation/request ID will be included for traceability?
  A feature without observability is not production-ready.

- What could go wrong and how you will handle it

- Feature flag needed?
  Required if: Large change, touches AI behaviour, or visible to users before fully tested

Do NOT make any changes yet. Show the full plan and wait for my approval.
```

**Atomic change check — you do this before approving:**

```
In one sentence: what is the single logical change this PR makes?
[write your sentence]

If you cannot describe it in one sentence → split it before approving.
```

**Once satisfied, confirm explicitly:**

```
Approved. Before you start:
Repeat back to me — which files will you change and in what order?
```

Make Copilot repeat the file list back. This is not ceremonial — it catches misunderstandings before a single file is touched.

---

## FC-3.5 — Backward Compatibility Check

**Skip if: Small change with no API or schema changes.**  
**Required for: Medium and Large changes.**

**Mode: Ask**

```
Based on the approved plan, answer these backward compatibility questions.
Do not touch any files.

API CONTRACT COMPATIBILITY
1. For every existing endpoint being modified:
   - What does the current request schema look like?
   - What will the new request schema look like?
   - Will existing callers sending the OLD request still get a valid response?

2. For every existing endpoint being modified:
   - What does the current response schema look like?
   - What will the new response schema look like?
   - Will existing callers expecting the OLD response fields still work?

DATABASE COMPATIBILITY
3. If there is a DB migration:
   - Can it run on the live database without downtime?
   - Nullable columns only? (safe) OR NOT NULL without default? (unsafe)
   - Rename or delete columns? (breaking)
   - What happens if the migration fails halfway?

4. During deployment overlap:
   - Old code running against new schema → does it work?
   - New code running against old schema → does it work (rollback scenario)?

AI BEHAVIOUR COMPATIBILITY
5. If AI prompts change:
   - Will the new prompt produce output in the same format?
   - Will existing code parsing the output still work?
   - Are there stored AI outputs that will be inconsistent with the new format?

For every breaking change found:
- Describe the exact breakage
- Propose the fix (additive migration, versioned endpoint, deprecation period)
```

**DB Migration safety classification:**

```
SAFE — run directly:
  ADD COLUMN nullable=True
  ADD TABLE (new table, no existing data affected)
  ADD INDEX CONCURRENTLY (Postgres)
  DROP INDEX

UNSAFE — use expand-contract across 3 separate PRs:
  ADD COLUMN nullable=False (no server_default)
  RENAME COLUMN
  CHANGE COLUMN TYPE
  DROP COLUMN / DROP TABLE
```

**For UNSAFE migrations — three separate PRs, in sequence:**

```
PR 1 — Expand: add new structure alongside old. Deploy. Verify old code still works.
PR 2 — Migrate: populate new structure from old data. Deploy. Verify both correct.
PR 3 — Contract: remove old structure. Deploy. Verify only new structure used.
```

**Migration rollback test — run on staging before production:**

```bash
alembic upgrade head     # run migration
alembic downgrade -1     # confirm it reverses cleanly
alembic upgrade head     # run again to confirm idempotent
# If downgrade fails → fix migration before touching production
```

---

## FC-3.6 — Feature Flag Strategy

**Skip if: Small or Medium change with no user-visible behaviour change.**  
**Required for: Large changes, any AI behaviour change, phased rollouts.**

**Mode: Plan**

```
Based on the approved plan, design a feature flag strategy.

1. What exactly does the flag control?
   "When FEATURE_<n>=true: [behaviour]. When false: [behaviour]."

2. Where does the flag live?
   Environment variable is correct for most cases.
   In backend/app/core/config.py add:
     feature_<n>_enabled: bool = False

3. Default value: OFF (false) — deploy does NOT activate the feature

4. Show the exact implementation in the feature code:
   from app.core.config import settings
   if not settings.feature_<n>_enabled:
       raise HTTPException(status_code=404, detail="Not available")

5. Rollout plan:
   Phase 1: Deploy with flag OFF → verify existing behaviour unchanged
   Phase 2: Enable for internal testing → verify new behaviour works
   Phase 3: Enable for all users → monitor errors and feedback
   Rollback: Set flag OFF → instant disable, no redeploy needed

6. Removal date:
   # TODO: Remove feature flag after [date] — flags are temporary

Show the plan. Do not write code yet.
```

---

## FC-3.7 — AI Prompt Change Risk Gate

**Skip if: This change does not touch any AI prompt files.**  
**Required for: Any change to prompt templates, model parameters, or output parsing.**

**Mode: Ask**

```
This change modifies AI prompts. Before proceeding, answer:

1. Which exact prompt files are changing?
   Show before and after for every template.

2. Does the new prompt produce output in the same format?
   Same structure, same field names, same response length range?

3. Risk classification:
   LOW:    fixes error · adds clarification · output format unchanged
   MEDIUM: changes tone, style, or reasoning · quality may vary
   HIGH:   changes output format · changes model parameters · adds/removes tools

4. Cost impact:
   Estimated tokens per request (input + output): [before] → [after]
   Requests per day at expected traffic: [N]
   Cost delta per day: ([after tokens] - [before tokens]) / 1000 × [price per 1K tokens] × [requests per day]
   Is there any risk of runaway token usage (loops, unbounded context, large documents)?

5. Mandatory eval sequence:
   Step 1: Record baseline eval score on current prompt
   Step 2: Write candidate prompt in a SEPARATE file (do not overwrite)
   Step 3: Run evals against candidate — no regressions allowed
   Step 4: Replace current prompt only after candidate passes
   Step 5: Bump PROMPT_VERSION, add change comment
   Step 6: Run evals one final time to confirm

HIGH RISK requires:
   → Feature flag (mandatory — no exceptions)
   → Staged rollout (internal first, then users)
   → Monitoring alert for quality regression
```

---

# PHASE 3 — IMPLEMENTATION

---

## FC-4 — Implement the Change

**Mode: Agent**

```
Implement the approved plan exactly as described and approved.

Strict file list — you may ONLY edit these files:
[list every file from the approved plan]

If you are about to edit anything not on this list, STOP and ask me first.

Implementation rules:
- Change one file at a time, in the approved order
- After every file: run ruff check and mypy — fix any errors before moving to the next file
- After every file: show me a diff of what changed
- Match the existing code style, naming conventions, and error handling patterns exactly
- Do not "clean up" anything not in the approved plan
- Do not improve anything not in the approved plan

Observability requirement — every new feature code path must include:
- Structured log at the entry point of the new feature (INFO level)
- Structured log at each key branching decision (DEBUG level)
- Error log with full context on every exception (ERROR level, include request ID)
- For performance-critical paths: timing metric or log with duration
- Correlation/request ID included in every log line within this feature
If you are about to complete a file without adding observability → stop and add it first.

If you notice something that should be improved but is not in the plan:
Say "I noticed [X] — should I add it?" and wait for my answer. Do not add it unilaterally.
```

**Stop work conditions — stop immediately if any of these occur:**

```
STOP if:
  ✗ An unexpected module is affected that was not in the impact analysis
  ✗ The actual code behaviour differs from what the plan assumed
  ✗ Tests fail repeatedly without a clear and understood cause
  ✗ You find shared state or hidden dependencies that change the blast radius

When stopped:
  Do not make any more changes.
  List every file changed so far.
  Re-run FC-2 (impact analysis) and FC-3 (planning) before continuing.
  Do not push forward through uncertainty.
```

**Diff size gate — pause at these checkpoints:**

```
Small change:   Review when ALL files are done (1 checkpoint)
Medium change:  Pause and review after every 3 files
Large change:   Pause and review after every 3 files
```

At each checkpoint:

```
Pause. We have completed [N] files.

Show me:
1. Every file changed so far and a one-line summary of each change
2. Total lines added and lines removed so far
3. Any unexpected discoveries during implementation

If total lines changed exceeds 300 — stop completely.
We need to split this into multiple smaller PRs before continuing.
```

**If Copilot goes off-scope:**

```
Stop immediately. Do not make any more changes.
List every file you have changed in this session,
including any not in the approved plan.
```

Then:

```bash
git checkout -- path/to/file.py   # revert one file
git checkout .                     # revert everything
```

Restart FC-4 with "Strict file list" enforced at the top of the prompt.

---

## FC-4.5 — Performance Impact Check

**Skip if: Small change.**  
**Required for: Medium and Large changes.**

**Mode: Ask**

```
Read all files changed in this feature.

Check for performance impact — answer each:

DATABASE QUERIES
1. Any new database query added to a path that previously had none?
2. Any query inside a loop? (N+1 — name the loop and the query)
3. Any large result set loaded into memory when only a subset is needed?
4. Any new query patterns that need an index that is missing?

AI CALLS
5. Any new AI call added to an existing endpoint?
   Estimated latency added (tokens × model speed)?
6. Any increase in token count sent to the model? By how much?
7. Any AI calls inside a loop or per-item processing?
8. Estimated cost delta per day at expected traffic?

ENDPOINT LATENCY
9. For every modified endpoint:
   Estimated response time before this change?
   Estimated response time after?
   Is the change acceptable given the SLA in requirements?

MEMORY
10. Any large data buffered in memory for the duration of a request?
    Is there a streaming alternative?

For every issue found:
- Describe concretely (not vaguely)
- Estimate impact: milliseconds added, queries added, tokens added, cost added
- Propose the fix

Critical performance issues must be fixed before proceeding.
Medium performance issues are noted and scheduled for a follow-up PR.
```

---

## FC-5 — Verify Existing Tests Pass

**Mode: Agent — all change sizes.**

```
Run all existing tests for the modules that were changed.

Command: pytest tests/ -v --tb=short

For every test that now fails, tell me:
1. The exact test name and failure message
2. Why it is failing:
   - Real regression? (our change broke something that worked)
   - Or does the test need updating to match intentional new behaviour?

If real regression → fix the IMPLEMENTATION, not the test.
If behaviour intentionally changed → update the test with a comment:
  # Updated: [date] — behaviour changed because [reason]

Rules:
- Do not mark any test as skip or xfail to make count green
- Do not delete a test to make count green
- Do not change assertion values without understanding why they changed

Report: X tests passing, 0 failing.
```

Zero regressions is non-negotiable. If you cannot reach zero, go back to FC-4 and fix the implementation.

---

# PHASE 4 — QUALITY & SHIP

---

## FC-6 — Write New Tests

**Mode: Agent — all change sizes.**

```
Read the changes made in FC-4.

Generate new tests covering the new feature.
Match existing test structure exactly:
- Same folder layout (tests/unit/ or tests/integration/)
- Same file naming pattern
- Same fixture patterns from conftest.py
- Same mock approach for AI APIs and external services

Write these test categories:

1. Happy path tests
   Every new code path added by this feature
   Exact inputs and expected outputs

2. Failure path tests
   Every new error condition introduced
   What happens when each new dependency fails
   What the API returns when validation fails

3. Observability tests
   Confirm structured logs are emitted at key decision points
   Confirm error logs include the correct context fields
   Confirm correlation/request ID appears in logs for this feature path
   Name these: test_<feature_name>_logs_<what_is_verified>

4. Backward compatibility tests (if API contracts changed):
   Test that OLD request format still returns a valid response
   Test that OLD response fields are still present in new response
   Name these: test_backward_compat_<what_is_preserved>

5. Performance regression tests (if performance-sensitive paths changed):
   Assert endpoint completes within expected SLA
   Name these: test_perf_<endpoint_name>_within_sla

6. Feature flag tests (if a flag was added):
   Test behaviour when flag is OFF: feature inaccessible
   Test behaviour when flag is ON: feature works correctly
   Test that flag=OFF does not break existing behaviour

7. Edge cases:
   None input where feature accepts optional data
   Empty string where string input is accepted
   Maximum allowed value for numeric input
   Concurrent calls if feature modifies shared state

8. AI-specific tests (only if this feature touches the AI layer):
   AI API returns unexpected response format
   AI API completely unavailable
   Token limit exceeded before the call
   Prompt injection attempt blocked or sanitised

9. One regression lock test:
   Name: test_<feature_name>_regression
   Must assert complete end-to-end behaviour of this feature
   Any future change that silently breaks this feature must fail this test

Run all tests — old and new together:
pytest tests/ -v --cov=backend --cov-report=term-missing

Report: X passing, 0 failing, coverage %.
```

---

## FC-7 — Review the Diff

**Mode: Plan first, then Agent.**

Use a second AI (Claude or ChatGPT) to review — Copilot cannot reliably review its own output.

**Quick version for Small changes:**

Paste changed files into Claude or ChatGPT with:

```
You are a senior engineer doing a strict code review.

Check ONLY the changed files for:
- Logic errors and correctness
- Security issues (unvalidated input, exposed secrets, missing auth)
- Missing error handling
- Missing observability (logging at key points, errors logged with context)

Flag: [CRITICAL] — breaks in production
      [HIGH] — significant risk

Do not comment on style, Medium, or Low concerns.
End with: VERDICT: PASS or VERDICT: FAIL
```

**Full version for Medium and Large changes:**

```
You are a senior engineer reviewing only the files changed in this feature.

Files changed: [list them]

For each changed file, check:

CORRECTNESS
- Does logic match what was planned and approved?
- Off-by-one errors or wrong conditions?
- Async/await used correctly?

ERROR HANDLING
- Consistent with the rest of this module?
- Same named exception classes used?
- Errors logged in same format?

OBSERVABILITY
- Is there structured logging at the entry point of every new feature path?
- Is there logging at each key branching decision?
- Are errors logged with full context (request ID, user ID, relevant state)?
- For performance-critical paths: is timing logged or a metric emitted?
- Is the correlation/request ID included in every log line?
- If any of these are missing → flag as [HIGH]

SECURITY
- Any new user input not validated?
- Any new data in API responses that should not be exposed?
- If AI prompts changed: user input sanitised before injection?
- Any new endpoints missing authentication?

BACKWARD COMPATIBILITY
- Does the old request format still work?
- Are all old response fields still present?
- Is DB migration safe to run on live data?

PERFORMANCE
- Any N+1 queries introduced?
- Any new AI calls on a hot path?
- Any AI cost increase that is not justified?
- Any synchronous I/O blocking async event loop?

FEATURE FLAG (if applicable)
- Default value OFF?
- Every new code path guarded by flag?
- Old behaviour fully preserved when flag=OFF?

AI LAYER (if this feature touches AI logic)
- Prompt template parameterised? No hardcoded values?
- Timeout and retry still in place on AI call?
- PROMPT_VERSION bumped if any template changed?
- Cost impact documented and acceptable?

TESTS
- New tests actually test new behaviour?
- Any new code path with no corresponding test?
- Observability tests present?
- Backward compat tests present if API changed?

Score: [CRITICAL] / [HIGH] / [MEDIUM] / [LOW]
End with: VERDICT: PASS (zero Critical, zero High) or VERDICT: FAIL
```

Then send to Copilot (Agent mode):

```
Fix all Critical and High issues from the review.
Do not touch anything outside the files already changed.
After every fix: run pytest tests/ -v and confirm tests still pass.
Show a diff of every change made.
```

---

## FC-8 — Pre-Commit Safety Check and Commit

**Part A — You run manually. No Copilot.**

```bash
# 1. See exactly what changed
git diff --stat          # file-level summary
git diff                 # full line-level diff

# 2. Confirm only planned files changed
git diff --name-only

# 3. Count total lines changed
git diff --shortstat

# 4. Check for anything that must never be committed
git diff | grep -E "print\(|debugger|TODO:|FIXME:|password|api_key|secret"

# 5. Confirm no test files were deleted
git diff --name-only --diff-filter=D | grep test
```

**Hard stops — do not proceed if any of these are true:**

```
Any file changed not in the approved plan   → investigate and revert
Total diff exceeds 300 lines               → split into multiple PRs
Any secret, password, or API key in diff   → remove it, rotate the key
Any test file deleted                      → restore it
Any print() or debug in production code    → remove it
Lint or type check fails                   → fix before committing
```

**Part B — Copilot final summary (Ask mode):**

```
Give me a final summary before I commit:

1. Every file changed — list them
2. Every new file created — list them
3. Every new test added — list them
4. All tests passing — confirm with exact count
5. No tests deleted or skipped — confirm
6. Scope stayed within approved plan — confirm. List anything changed outside plan.
7. This PR is atomic — one logical change, one rollback boundary — confirm

8. DB migration created?
   If yes: tested with upgrade AND downgrade on staging?
   Is alembic upgrade head part of the deploy process?

9. AI prompts changed?
   If yes: PROMPT_VERSION bumped with change comment?
   Eval suite passed with no regressions?
   Cost impact per day documented?
   If HIGH RISK: feature flag in place?

10. Backward compatibility confirmed:
    API contracts changed: old callers still supported?
    DB migration: SAFE or UNSAFE? Expand-contract followed if UNSAFE?

11. Performance impact confirmed:
    No new N+1 queries? No unacceptable latency added?

12. Observability confirmed:
    Structured logging at key decision points?
    Error logging with context on all new exception paths?
    Correlation/request ID included in feature log lines?

13. Feature flag in place (if Large or HIGH RISK prompt change):
    Default OFF? Old behaviour preserved when OFF?

14. Rollback plan — state the exact sequence:
    With flag: set FEATURE_<n>=false → restart → instant disable
    Without flag: git revert <commit> → push → redeploy
    With DB migration: alembic downgrade -1 BEFORE reverting code

15. Anything I should manually verify in the running app before merging?
```

**PR Description — fill this before committing:**

```markdown
## What changed
[one paragraph — what this PR does]

## Why it changed
[the business or technical reason — link to issue or requirement]

## Risk level
[Low / Medium / High] — [one sentence justifying the classification]

## Observability added
[what logging, metrics, or tracing was added for this feature]

## Testing done
- [ ] Unit tests added and passing
- [ ] Integration tests passing
- [ ] Backward compat tests (if API changed)
- [ ] Feature flag tests (if flag added)
- [ ] Manually tested locally
- [ ] Manually tested on staging

## Rollback plan
[exact steps: feature flag / git revert / alembic downgrade — whichever applies]

## Areas to watch after deploy
[what to monitor, what errors would indicate a problem, how long to watch]
```

**You commit. Copilot never commits.**

```bash
git add .
git commit -m "feat(<module>): <what changed and why>

- atomic: [one sentence — the single logical change this PR makes]
- <specific change 1>
- <specific change 2>
- migration: <schema change description — if applicable>
- prompt: bumped to v[N] — <reason> — risk: LOW/MEDIUM/HIGH — cost delta: $X/day
- flag: FEATURE_<n> defaults to OFF

Closes #<issue number>"

git push origin feature/<your-feature-name>
```

---

## FC-8.5 — Rollback Validation

**Skip if: Small change with no DB migration and no feature flag.**  
**Required for: Medium and Large changes — before merging to main.**

Do not assume rollback works. Test it before the change is in production.

**You do this manually on staging. No Copilot.**

```
ROLLBACK SIMULATION CHECKLIST
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

If DB migration exists:
  1. Run migration on staging:  alembic upgrade head
  2. Verify app works correctly with new schema
  3. Simulate rollback:         alembic downgrade -1
  4. Verify old code still works against rolled-back schema
     → If old code fails: the migration is not safely reversible
     → Fix the migration before touching production
  5. Run forward again:         alembic upgrade head
  6. Confirm the cycle is clean and idempotent

If feature flag exists:
  1. Deploy with flag OFF
  2. Verify existing behaviour is completely unchanged
  3. Enable flag: set FEATURE_<n>=true, restart app
  4. Verify new feature works correctly
  5. Disable flag: set FEATURE_<n>=false, restart app
  6. Verify existing behaviour is restored — no trace of new feature visible
     → If old behaviour is not fully restored: the flag is not safe
     → Fix the flag guard before merging

Data corruption check:
  → Does enabling then disabling the flag leave any data in an inconsistent state?
  → Does running then rolling back the migration leave data in an inconsistent state?
  → If yes to either: this is not safe to ship without a fix

RESULT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PASS: rollback simulated successfully, old behaviour confirmed restored
      → Proceed to FC-8.7 staging smoke tests

FAIL: rollback leaves system in broken or inconsistent state
      → Do NOT merge → fix the issue → re-run rollback validation
```

---

## FC-8.7 — Staging Verification

**Skip if: Small change with no new endpoints, no schema changes, no AI changes.**  
**Required for: Medium and Large changes — after rollback validation passes.**

**You do this manually. No Copilot.**

```bash
# 1. Deploy to staging
# Railway:  git push staging feature/<n>:main
# Render:   trigger manual deploy from staging branch

# 2. Confirm app started correctly
curl https://your-staging-app.com/health
# Expected: {"status": "healthy", "database": "connected"}

# 3. If DB migration ran — verify schema applied, existing data intact
```

**Manual smoke tests — run every applicable one:**

```
Existing features (confirm nothing broke):
  ✓ Most common user action works correctly
  ✓ Most-used API endpoint returns valid response

New feature:
  ✓ Valid input → correct response
  ✓ Invalid input → correct error (not 500)
  ✓ Logs show expected structured entries for the new feature path

If DB migration ran:
  ✓ New record can be created with new schema
  ✓ Existing record still readable correctly

If AI prompt changed:
  ✓ Send 3 real inputs manually and read responses
  ✓ Output format matches what the parser expects
  ✓ Response quality acceptable on real inputs

If feature flag added:
  ✓ Flag=OFF: old behaviour works, new feature inaccessible
  ✓ Enable flag → new feature works → disable flag again
```

```
PASS → merge to main → deploy to production
FAIL → do NOT merge → fix → re-run staging verification
```

---

## FC-9 — Post-Deployment Verification

**All change sizes — run immediately after deploying to production.**

This is active verification, not passive monitoring. Do not skip it and rely on dashboards.

**You do this manually. No Copilot.**

```
IMMEDIATELY AFTER DEPLOY (within 5 minutes)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Health check:
   curl https://your-production-app.com/health
   Expected: {"status": "healthy"} — if not, investigate immediately

2. Verify 3 core user flows manually:
   ✓ [Your most critical user action] — does it work correctly?
   ✓ [Second most critical user action] — does it work correctly?
   ✓ [The new feature specifically] — does it work correctly with real input?

3. Check error logs (not metrics — the actual logs):
   Look for any new ERROR or CRITICAL entries since deploy
   If unexpected errors appear → investigate before continuing

4. Check latency:
   Is p95 response time similar to before the deploy?
   Any endpoint that is significantly slower is a signal

MONITOR WINDOW (after initial verification)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Small change:   Stay available for 15 minutes
Medium change:  Stay available for 1 hour
Large change:   Stay available for 24 hours (set alerts)

During window, watch for:
  ✓ Error rate: not higher than pre-deploy baseline
  ✓ Latency: p95 not increased unexpectedly
  ✓ AI quality (if AI changed): responses acceptable on real traffic
  ✓ Feature flag (if enabled): new feature behaving correctly
  ✓ Cost (if AI changed): per-request cost within expected range

ROLLBACK TRIGGER — act immediately if ANY of these appear:
  ✗ Error rate rises more than 10% above baseline
  ✗ p95 latency doubles or exceeds the SLA
  ✗ Any CRITICAL error in logs not seen before deploy
  ✗ Users reporting broken core functionality
  ✗ AI cost spiking unexpectedly

ROLLBACK EXECUTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
With flag:      Set FEATURE_<n>=false → restart → instant disable
Without flag:   git revert <merge-commit> → push → redeploy
With migration: alembic downgrade -1 FIRST → then revert code (always)
```

---

# Quick Reference Card

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CLASSIFY FIRST
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Small    1–3 files · no endpoints · no schema · no AI
Medium   4–10 files · may add 1 endpoint · additive API
Large    10+ files · breaking contract · migration · AI

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
HOTFIX MODE (emergency only)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Only if: prod broken + cause known + fix is 1–3 lines
Steps: branch → fix → test → review → staging → ship
Mandatory follow-up PR within 24 hours

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 0 — ANALYSIS         Required for unfamiliar code
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RA-0   Setup               One-time per repo
RA-1   Trigger checklist   Before every analysis run
RA-2   Run analyzerepo     Agent mode · #codebase context
RA-3   Targeted depth      Run for your specific area
RA-4   Save to file        Populate ARCHITECTURE_ANALYSIS.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 1 — PREP             All sizes
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FC-0   Branch + baseline   git checkout -b · pytest green
FC-0.5 Ownership           Assign owner · reviewer · rollback owner
FC-1   Codebase map        Ask · Medium + Large (or stale analysis)
FC-1.5 Requirement valid.  Ask · Medium + Large · stop if unclear
FC-2   Impact analysis     Ask · quick or full

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 2 — PLAN             All sizes
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FC-3   Plan + approve      Plan · atomic check · observability req · repeat-back
FC-3.5 Compat check        Ask · Medium + Large
FC-3.6 Feature flag        Plan · Large + AI behaviour changes
FC-3.7 AI risk gate        Ask · cost impact · any AI touch

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 3 — IMPLEMENT        All sizes
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FC-4   Implement           Agent · one file · observability · stop work conditions
FC-4.5 Perf check          Ask · AI cost delta · Medium + Large
FC-5   Existing tests      Agent · zero regressions mandatory

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 4 — QUALITY & SHIP   All sizes
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
FC-6   New tests           Agent · observability tests included
FC-7   Diff review         Dual-model · observability checked · fix Critical + High
FC-8   Pre-commit          You (git diff) + Ask (summary) + PR description · You commit
FC-8.5 Rollback test       You · simulate on staging · Medium + Large
FC-8.7 Staging smoke       You · Medium + Large
FC-9   Post-deploy         You · active verification · all sizes · rollback trigger ready

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RULES THAT NEVER CHANGE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Understand first       Run repo analysis before touching unfamiliar code
Validate requirement   Confirm you're building the right thing before building it
Assign ownership       Owner · reviewer · rollback owner — clear before any code
Atomic PR              One logical change · one sentence · one rollback unit
Plan before agent      Never implement without an approved and repeated-back plan
Strict file list       Stop Copilot the moment it touches an unplanned file
Stop work              Re-plan when unexpected complexity surfaces — do not push through
One file at a time     Lint + type check after every file before moving on
Observability          Every new feature path must log, trace, and surface errors
Zero regressions       Fix implementation — never fix the test to make count green
Git diff first         Run manually before Copilot final summary
PR description         Fill it before committing — not optional
You commit             Copilot never commits — you review diff and commit
Test rollback          Simulate before merging, not after something breaks
Staging before main    Deploy and verify before merging (Medium + Large)
Active post-deploy     Verify manually — do not rely only on dashboards

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ROLLBACK SEQUENCES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
With flag:      Set FEATURE_<n>=false → restart → instant disable
Without flag:   git revert <merge-commit> → push → redeploy
With migration: alembic downgrade -1 FIRST → then revert code (always)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PRE-COMMIT HARD STOPS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Unplanned file changed          → investigate and revert
Total diff over 300 lines       → split the PR
Secret or key in diff           → remove it, rotate the key
Test file deleted               → restore it
print() or debug in prod code   → remove it
Lint or type check fails        → fix before committing

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
BACKWARD COMPATIBILITY — SAFE VS UNSAFE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Safe:   Add new optional fields · add nullable columns · add new endpoints
Unsafe: Remove/rename fields · add NOT NULL columns · change field types
Breaking changes require versioned endpoint or deprecation period

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
AI PROMPT RISK LEVELS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LOW     Fix error · clarify · output format unchanged
MEDIUM  Change tone or reasoning · quality may vary
HIGH    Change output format · change model params · add/remove tools
        → Feature flag mandatory + staged rollout + monitoring alert
Always document cost delta per day before merging any AI change
```

---

*Python + FastAPI + GitHub Copilot*  
*Understand before you change. Validate before you build. Ship with confidence.*
