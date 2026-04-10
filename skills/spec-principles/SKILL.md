---
name: spec-principles
description: Apply core specification principles: IS/IS-NOT distinction, constrain-design-open-implementation, and anti-pattern detection. Use when creating, reviewing, or evaluating any specification document.
---

# Specification Principles

Foundational knowledge for working with software specifications.

## Applicability Rubric

| Condition | Pass | Fail |
|-----------|------|------|
| New specification needed | Feature/project has no SPEC.md | Spec already exists and is complete |
| Specification quality concern | Existing spec is unclear or incomplete | Spec is well-structured and implementable |
| Design direction guidance | Implementers need design direction | Implementation path is already clear |
| Multi-person collaboration | Multiple people implement from spec | Solo development, no coordination needed |

**Apply when**: Any condition passes

## Core Principles

### What Makes a Good Specification

A specification describes the **target state**—what the system looks like when complete.

| Specification IS | Specification is NOT |
|-----------------|---------------------|
| Target state description | Implementation plan |
| Problem context (why it exists) | Decision rationale (why A over B) |
| Design decisions (what to build) | Implementation choices (how to build) |
| Declarative statements and structured formats | Prose narrative explanations |

### What a Specification Review IS / IS NOT

| Review IS | Review is NOT |
|-----------|---------------|
| Evaluating quality of declared scope | Suggesting new scope to add |
| Checking what's written is correct and unambiguous | Checking what's not written |
| Identifying defects in existing content | Proposing "good to have" additions |
| Helping narrow unclear boundaries | Expanding boundaries to cover more |
| Assessing against the user's target quality level | Pushing all specs toward Complete level |

**Specify vs Leave Open** (for writing decisions; see spec-quality Balance Check for review verification):

| Question | Specify | Leave to Implementer |
|----------|---------|----------------------|
| User-visible? | Yes | No |
| Affects module contracts? | Yes | No |
| Could cause inconsistent interpretation? | Yes | No |
| Internal algorithm or data structure? | Only if cross-implementer consistency required | Yes (default) |

**Over-specified vs Properly Constrained:**

| Aspect | Over-specified | Properly Constrained |
|--------|---------------|----------------------|
| Error handling | "Use try-catch with retry 3 times" | "Retry transient failures; report permanent failures to user" |
| Data storage | "Store in PostgreSQL users table" | "Persist user profile across sessions" |
| UI layout | "Use 16px grid with flexbox" | "Display items in a scannable list with clear hierarchy" |

### Constrain Design, Open Implementation

- Specify all user-visible decisions
- Leave internal implementation choices to implementers unless cross-implementer consistency requires shared conventions
- If an implementer must guess a design decision, the specification is incomplete

### Three-Layer Overview

Complete specifications address three layers:

| Layer | Purpose | Key Question |
|-------|---------|--------------|
| Intent | Why this exists | What problem for whom? |
| Design | What to build | What boundaries, interfaces, behaviors? |
| Consistency | How to stay unified | What patterns for similar problems? |

### Anti-Patterns (prevention; see spec-quality Common Problems for diagnostic use)

| Anti-Pattern | Symptom | Why It Fails | Remedy |
|-------------|---------|-------------|--------|
| Explanatory notes | `Note:` appears in spec | If clarification is needed, the spec itself is unclear | Rewrite the statement directly |
| Phase markers | `(Future)`, `(v2)` in spec | Spec describes target state, not phases | Remove or split into separate spec |
| Optional markers | `(Optional)` in spec | Either required for target state or not | Move to Non-goals or make it required |
| Over-specification | Algorithm details, framework choices | Constrains implementer without design benefit | Replace with observable behavior |
| Prose narrative | "First we do X, then Y happens" | Mixes process with target state | Use declarative statements or structured formats (Context → Action → Outcome) |
| Vague language | "Handle appropriately", "reasonable time" | Different implementers interpret differently | Use specific values or measurable criteria |

## Completion Rubric

### Before Writing/Reviewing

| Criterion | Pass | Fail |
|-----------|------|------|
| Problem understanding | Can articulate the problem in one sentence | Problem is vague or undefined |
| User identification | Know who will use this | Users not identified |
| Scope definition | Clear what is included and excluded | Scope is open-ended |

### During Writing/Reviewing

| Criterion | Pass | Fail |
|-----------|------|------|
| Declarative statements | Spec uses "system does X" or structured formats | Spec uses prose narrative "first... then..." |
| Design vs implementation | Only observable behaviors specified | Internal details included |
| Anti-pattern free | No explanatory notes, phase markers, vague language | Anti-patterns present |
| Specify vs Leave Open | Each decision tested against the decision table | Decisions not evaluated |

### After Writing/Reviewing

| Criterion | Pass | Fail |
|-----------|------|------|
| Implementer test | An implementer can build without clarifying questions | Ambiguity remains |
| Compatibility test | Two implementers would produce compatible results | Interpretation varies |
| Balance check (spec-quality) | No over-specification or under-specification detected | Imbalance found |
