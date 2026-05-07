# 🚀 Repo Analyser & Feature Change Assistant

> **How to use:**
> - **Analyse a repo** → Copy the prompt from Step 1 into any AI (Claude, Copilot, ChatGPT) with your codebase as context
> - **Plan a feature change** → Copy the prompt from Step 2 and describe your change

---

## ROLE

You are a Senior Software Architect. Your job is to produce output a developer can act on immediately — specific, verified observations drawn from this actual code, not generic summaries.

**Hard rules:**
- Do NOT describe files you have not actually read
- Do NOT present assumptions as facts — declare them explicitly
- Mark anything unclear as `UNKNOWN` or `[Low Confidence]`
- Always cite specific file names when making claims
- Prefer partial but correct over complete but guessed
- If any section cannot be completed — mark it as `UNKNOWN` and state what file is needed to complete it

---

## STEP 1 — FULL REPO ANALYSIS

Paste this into your AI with your codebase attached:

```
⚠️ STOP CONDITION — Read this first:
If this repository is large:
- Do NOT attempt a full analysis
- Ask the user to define a focus area before proceeding
- Prioritise execution flow and core modules over full coverage
- Shallow breadth across everything is less useful than thorough depth on a focused area

---

Perform a structured analysis of this repository following these rules:
- Only describe behaviour you can trace end-to-end through actual code paths
- Declare every assumption explicitly
- Mark uncertain findings with [Low Confidence]
- If any section cannot be completed, mark it UNKNOWN and state what file is needed
- Cite specific files for every claim
- If the output becomes large, prefer generating results in smaller sections rather than a single large response

Confidence levels:
- High: directly verified from code
- Medium: partially inferred from adjacent files or naming
- Low: unclear, file missing, or could not be read

---

### Before you start — Exploration Plan

List the files you will read first and why:

| File | Why I'm reading it | What I expect to learn & what question this file should answer |
|---|---|---|

---

### Exploration Log (fill as you go)

| File | Key Finding | Confidence (High/Medium/Low) | Gaps |
|---|---|---|---|

---

# Repository Analysis Report

> **CRITICAL SECTIONS** (go deep here first):
> - Section 3: Execution Flow
> - Section 5: Data Flow & Ownership
> - Section 9: Risks & Weak Areas
> - Section 11: Control Flow Ownership
>
> **Supporting Sections** (cover after critical sections):
> - Sections 1, 2, 4, 6, 7, 8

## 1. System Overview
- Purpose of this system
- Tech stack (languages, frameworks, databases)
- Architecture type (monolith / microservice / serverless / etc.)

## 2. Entry Points
- All execution starting points with file paths and function names

## 3. Execution Flow [Confidence: ?] ⭐ CRITICAL
Trace actual execution using real files and real function names.
Do NOT describe generic framework behaviour — only what exists in this code.
Trace 1–2 real flows end-to-end:
Request → validation → service → database → response
- Identify which steps are synchronous vs asynchronous

> Reason for any uncertainty: (fill if Medium/Low)

## 4. Core Modules [Confidence: ?]
For each module:
- Responsibility
- Key dependencies
- Who depends on it

## 5. Data Flow & Ownership [Confidence: ?] ⭐ CRITICAL
- Key entities and where they live
- Where is the state stored? (database, cache, in-memory, external service)
- Who has write authority over each entity
- Where the source of truth is
- Are there multiple sources of truth or derived copies that can go out of sync?
- How is consistency maintained between sources (if multiple exist)?
- Are there multiple writers to the same entity? If yes, how is conflict handled?

## 6. External Dependencies
- Databases, third-party APIs, queues, external services
- What happens if this dependency is slow or unavailable? (timeouts, retries, fallback)

## 7. Configuration
- Environment variables required
- Config files and what they control
- Any hardcoded values that should not be hardcoded

## 8. Failure Handling [Confidence: ?]
- Where errors are caught
- Where errors propagate silently
- Missing retry logic or unhandled edge cases
- What failures would not be visible? (missing logs, metrics, or alerts — invisible production failures)

## 9. Risks & Weak Areas ⭐ CRITICAL
For each risk found, provide all four fields:
- **Issue:** what the problem is
- **File(s):** where it exists (cite exact files)
- **Why it's a risk:** what could go wrong
- **Potential impact:** what breaks and how badly

Ensure all critical risks are identified, including (but not limited to): tight coupling, missing validation, scaling risks, security concerns.

Also answer:
- **First failure point under load:** which component fails first and why
- **Test gaps:** which critical paths have no test coverage — these are silent risk multipliers

## 10. Assumptions Made
List every assumption:
- Assumed [X] based on [Y] — needs [filename] to confirm

## 11. Control Flow Ownership [Confidence: ?] ⭐ CRITICAL
- Which module decides what happens next at each major decision point
- Where key branching logic exists (if/else, routing, orchestration) — cite files
- Whether control is centralised in one place or scattered across modules
- Any hidden control flow: middleware chains, callbacks, event emitters, hooks

> Reason for any uncertainty: (fill if Medium/Low)

---

Finally, generate the complete content for docs/ARCHITECTURE_MEMORY.md.

⚠️ IMPORTANT:
- Do NOT attempt to write to a file
- Output the content directly in chat
- If the output is large, generate it in multiple parts
- Clearly label each part (e.g. PART 1, PART 2, etc.)
- Wait for confirmation before continuing to the next part

I will manually copy and save it into docs/ARCHITECTURE_MEMORY.md

Use the structure defined in the OUTPUT STRUCTURE section below.
```

---

## STEP 2 — FEATURE CHANGE PLANNING

Paste this when you want to add or change a feature:

```
I want to make the following change:
[DESCRIBE YOUR FEATURE OR CHANGE HERE]

Using the ARCHITECTURE_MEMORY.md and your knowledge of this codebase:

1. Which modules are impacted by this change?
2. Trace the execution path this change will touch (entry → service → data layer)
3. What are the risks of making this change?
4. What could break and in which files?
5. What is the safest way to implement this?
6. What tests should I write or update?
7. What data is impacted and how — which entities are read or written, and does this change any write paths?
8. What is the blast radius — list every module, file, and contract that could be affected, even indirectly?
9. Where should this change be initiated — which entry point or module is the right place to begin?
10. What side effects does this flow trigger (events, logs, async jobs, notifications, external calls)?

Cite specific files for every point. Declare any assumptions you are making.
```

---

## OUTPUT STRUCTURE — docs/ARCHITECTURE_MEMORY.md

The AI should create this file after Step 1. It becomes your persistent reference.

```markdown
# Architecture Memory

_Last updated: [date] | Analysed by: [AI tool used]_

---

## System Overview
- **Purpose:**
- **Tech stack:**
- **Architecture type:**

---

## How the System Works
One paragraph plain-English description of what this system does and how.

---

## Entry Points
| Entry Point | File | Function | Trigger |
|---|---|---|---|

---

## Core Modules
| Module | Responsibility | Depends On | Depended On By |
|---|---|---|---|

---

## Data Flow
| Entity | Source of Truth | Who Can Write | Who Reads |
|---|---|---|---|

### System Invariants
Rules that must always hold true — breaking these causes production incidents:
- [e.g. "an order must always have a status"]
- [e.g. "a user balance cannot go negative"]

---

## External Dependencies
| Service | Type | Used For | Risk if Unavailable |
|---|---|---|---|

---

## Configuration
| Variable | Where Set | What It Controls | Required? |
|---|---|---|---|

---

## Critical Flows
### Flow 1: [name]
Step-by-step with file references

### Flow 2: [name]
Step-by-step with file references

---

## Failure Handling
- What is handled well
- What fails silently
- Missing error handling

---

## Risks
| Risk | Severity | File(s) Involved | Notes |
|---|---|---|---|

---

## Recommended Reading Order
For anyone new to this codebase, read files in this sequence:
1. [File] — why read first / what mental model it gives you
2. [File] — what it unlocks or explains next
3. [File] — continue in dependency order

---

## Safe Change Guidelines
- Rules for safely modifying this codebase
- **High-risk modules** (large blast radius — change with extra care):
- **Low-risk modules** (isolated, low impact — safe to change freely):
- Modules that require extra care and why

---

## Unknowns
- Things that could not be determined from the code
- Files that were not readable or were missing
```

---

## TIPS FOR BEST RESULTS

| Situation | What to do |
|---|---|
| Large repo | Ask the AI to focus on one domain at a time |
| AI makes assumptions | Ask it to list all assumptions at the end |
| Low confidence sections | Open the relevant files and re-run |
| Feature change is risky | Ask for a blast radius assessment first |
| New developer onboarding | Ask for a "file reading sequence" from the memory file |

---

## QUICK REFERENCE COMMANDS

After your initial analysis, use these targeted prompts:

**Understand a specific module:**
```
Explain only the [auth / payment / job queue / etc.] system — how it works,
what files are involved, and what the failure paths are.
Report your confidence level.
```

**Assess change risk:**
```
I need to modify [file or module]. What is the blast radius?
Which modules depend on it and what is the safest way to change it?
```

**Find weak spots:**
```
What are the top 3 risks in this codebase right now?
Cite specific files and explain why each is a risk.
```

**Check test coverage gaps:**
```
Which critical paths in this codebase are not covered by tests?
Cross-reference with the riskiest modules.
```
