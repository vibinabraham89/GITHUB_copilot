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
