GitHub Copilot — Existing Project Feature Change Workflow

The 3 modes
Ask mode   → Copilot reads and explains. Touches zero files.
Plan mode  → Copilot proposes a plan. You approve before anything happens.
Agent mode → Copilot executes. Creates and edits files autonomously.
Switch modes using the dropdown at the top of the Copilot Chat panel (Ctrl+Shift+I).

Before opening Copilot — you do these manually
bash

# 1. Create a branch — never work on main directly
git checkout -b feature/<your-feature-name>

# 2. Confirm clean baseline
git status        # should show nothing uncommitted

# 3. Confirm all tests pass before you touch anything
npm test          # must be fully green
```

If tests are already failing, stop. Fix the baseline first. Never start on a broken codebase — you will not know later whether you broke something or it was already broken.

---

## Step 1 — Understand what exists

**Mode: Ask**
```
Read the src/ folder of this project.

Give me a codebase map:
- Every module that exists and what it does
- The naming conventions used (files, functions, variables)
- The error handling pattern used consistently
- The logging approach — which logger, what format
- How external dependencies are accessed (DB, APIs, cache)
- The test structure and what coverage currently exists

Do NOT suggest any changes.
Just map what is here.
```

Read the output. Correct anything Copilot got wrong about your codebase. This is the foundation for everything that follows. If Copilot misunderstands your codebase here, every subsequent step will be wrong.

---

## Step 2 — Impact analysis

**Mode: Ask**
```
I want to add this feature:

[describe your feature change in plain language]

Based on what you just read about this codebase,
answer these questions. Do NOT touch any files.

1. Which existing files will need to change?
   List every file and the specific reason.

2. Which existing files could be affected indirectly?
   Things that call or depend on the files above.

3. Which existing tests cover the code being changed?
   List the test files and what they currently verify.

4. Does this change require a database migration?
   If yes, what schema changes are needed?

5. Does this change affect any API request or 
   response shapes?

6. What is the riskiest part of this change?
   Where is the most likely place something breaks?

Give me the analysis only. No solutions yet.
```

Read this carefully. It will frequently reveal that a small-looking change touches more than expected. That is the point — better to know now than after agent mode has already edited five files.

---

## Step 3 — Propose and approve the plan

**Mode: Plan**
```
Based on your impact analysis, propose the exact change plan.

For each file that needs to change:
- What currently exists in that file
- What you will specifically add, change, or remove
- Why that change is needed

Also tell me:
- The exact order you will make these changes
- Whether a DB migration is needed and what it looks like
- What could go wrong and how you will handle it

Do NOT make any changes yet.
Show me the full plan and wait for my approval.
```

Review the plan carefully. If anything looks wrong, say so now. Ask questions in Ask mode if you need clarification on anything. Once you are satisfied with the plan, say:
```
Approved. Do not start yet.
Confirm back to me: which files will you change
and in what order?
```

Make Copilot repeat the plan back. This confirms it understood correctly before a single file is touched.

---

## Step 4 — Implement the change

**Mode: Agent**
```
Execute the approved plan.

Strict rules during implementation:
- Change only the files listed in the approved plan
- If you find yourself wanting to change something 
  outside the plan, STOP and tell me first
- Match the existing code style in every file exactly
  Do not introduce new patterns
- After editing each file, run the TypeScript compiler
  Fix all type errors before moving to the next file
- Do not touch any test files in this step

After each file, tell me:
- Name of the file just changed
- A one-line summary of what changed
- Name of the next file you will change

If you encounter anything unexpected, stop immediately
and describe what you found before continuing.
```

**Critical rule:**
If Copilot says anything like "I also noticed X could be improved" or "I refactored Y while I was here" — stop it immediately with:

```
Revert that change. Only make the changes in the approved plan.
Nothing outside the plan.
```

Scope creep from agent mode on existing projects is how you introduce subtle bugs in code you never intended to touch.

---

## Step 5 — Verify existing tests still pass

**Mode: Agent**
```
Run all existing tests for the modules that were changed.

For every test that now fails:
- Show me the exact failure message
- Tell me why it is failing
  Is it because our change broke something real?
  Or because the test expectation needs to update
  to match the new correct behaviour?

If the failure reveals a real bug in our implementation:
- Fix the implementation, not the test

If the test expectation legitimately needs to change
because the behaviour intentionally changed:
- Update the test and add a comment explaining why
  the expected behaviour changed

Do not mark any test as skipped or pending.
Every existing test must pass before we move forward.

Report the final count:
X tests passing, 0 failing.
```

Zero regressions is non-negotiable. If you cannot get to zero, go back to Step 4 and fix the implementation. Do not paper over broken tests.

---

## Step 6 — Write new tests

**Mode: Agent**
```
Read the changes made in Step 4.

Generate new tests that cover:

1. The new behaviour introduced by this feature
   - Happy path for every new code path added
   - Failure path for every new error condition added

2. Edge cases specific to this feature:
   [describe the edge cases specific to your change]
   Examples:
   - Empty input
   - Maximum allowed value
   - Invalid data type
   - Concurrent calls to the same resource

3. One regression test that explicitly verifies
   this feature works end to end
   so it can never silently break in the future

Write all new tests into the same test folders
as the existing tests for these modules.
Follow the exact same test file naming and
structure that already exists in this project.

Run all tests — old and new together.
Report the final count and coverage percentage.
```

---

## Step 7 — Review the diff

**Mode: Plan first, then Agent**

Plan mode:
```
Review only the files that were changed in this feature.

[list the changed files from Step 4]

For each changed file check:
- Does it follow the existing patterns in this codebase?
- Are there any bugs introduced?
- Any unhandled edge cases?
- Any security concern?
  New input that is not validated?
  New data that should not be exposed?
- Is the error handling consistent with the rest
  of the codebase?
- Is the logging consistent with the existing format?

Risk level for each issue: Critical / High / Medium / Low

Review only the changed files.
Do not comment on anything outside the diff.
Wait for my response before fixing anything.
```

Read the report. Then switch to Agent mode:
```
Fix all Critical and High issues.
Do not touch anything outside the already-changed files.
Run all tests after every fix.
Confirm all tests still pass when done.
```

---

## Step 8 — Final check and commit

**Mode: Ask**
```
Give me a final summary before I commit:

1. Every file that was changed — list them
2. Every new file that was created — list them
3. Every new test that was added — list them
4. Confirm all tests are passing
5. Confirm no tests were deleted or skipped
6. Did the scope stay within the original plan?
   Was anything changed that was not in the plan?
7. Is there anything I should manually verify
   in the running app before merging this?
Read the summary. If scope drifted or anything looks unexpected, investigate before committing.
Then you commit manually:
bashgit add .
git commit -m "feat(<module>): <what changed and why>

- <specific change 1>
- <specific change 2>
- <db migration: what changed if applicable>

Closes #<issue number if applicable>"

git push origin feature/<your-feature-name>
```

---

## If agent goes off-scope mid-run

This will happen. The moment you see Copilot editing a file that was not in the approved plan:
```
Stop immediately.
Do not make any more changes.

List every file you have changed so far in this session,
including any that were not in the approved plan.
Then decide: if the out-of-scope changes are harmless, you can keep them. If they are not, revert the entire branch and start Step 4 again with a stricter prompt.
bash# Revert all changes and start implementation again
git checkout .
```

---

## Quick reference
```
BEFORE COPILOT   You        Create branch · clean baseline · tests green
Step 1           Ask        Map the codebase
Step 2           Ask        Impact analysis for your feature
Step 3           Plan       Propose plan · you approve
Step 4           Agent      Implement · one file at a time
Step 5           Agent      Existing tests must pass · zero regressions
Step 6           Agent      New tests for new behaviour
Step 7           Plan+Agent Review diff · fix Critical and High
Step 8           Ask+You    Final check · you commit manually
