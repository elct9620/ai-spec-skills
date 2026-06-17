---
name: spec-methodology
description: Write and improve specifications using progressive approach (Intent, Scope, Behavior, Refinement). Use when creating new specs, improving existing ones, or handling uncertainty and content splitting.
---

# Specification Methodology

Operational methods for creating, reviewing, and maintaining specifications.

## Applicability Rubric

| Condition | Pass | Fail |
|-----------|------|------|
| Writing new spec | Creating spec from scratch | Spec already written |
| Improving existing spec | Gaps identified in current spec | Spec passes all quality checks |
| Reviewing spec | Assessing spec for implementation readiness | No review needed |
| Splitting content | Spec growing beyond manageable size | Spec is concise and focused |

**Apply when**: Any condition passes

## Core Principles

### Progressive Approach

Apply to both writing new specifications and improving existing ones. When improving, identify current phase and proceed from there.

| Phase | Focus | Output | Confirm |
|-------|-------|--------|---------|
| 1. Intent | Why and for whom | Purpose, Users, Impacts | Purpose + Users stated |
| 2. Scope | What's included | Feature list, User journeys (Context → Action → Outcome) | List complete |
| 3. Behavior | How it works | Feature behaviors, Error scenarios | Behaviors + Errors defined, implementable |
| 4. Refinement | Quality | Patterns, Contracts, Terminology | Contracts, Terms, Patterns as needed |

**Rules:**
- Do not write Phase 3 details until Phase 2 is confirmed
- Phase 2 defines user-facing flows; Phase 3 defines feature behaviors and error scenarios
- Return to earlier phases when new understanding emerges
- Specification reaches Minimal quality level after Phase 3 (all Required Y)

### Phase Transition Guide

| Transition | Advance When | Stay/Return When |
|-----------|-------------|-----------------|
| Intent → Scope | Purpose and Users stated; stakeholders agree on both | Purpose unclear; users not validated |
| Scope → Behavior | Feature list confirmed; user journeys cover all impacts | New features discovered; flows incomplete |
| Behavior → Refinement | Behaviors and Errors defined, implementable; all features covered | Undefined scenarios found; implementer has questions |
| Refinement → Done | Target quality level met; Balance Check passes | Consistency gaps; terminology conflicts |

### Rewrite Over Append

When improving an existing spec, **rewrite the original paragraph** rather than append clarifications, Notes, or counter-statements. The spec is a single source of truth: accreting content over the original creates contradictions, doubles reading load, and leaves thinking traces (see spec-principles Anti-Patterns: Thinking traces, Explanatory notes).

| Situation | Do | Don't |
|-----------|-----|-------|
| Existing statement no longer fits intent | Rewrite that statement to express the new target state | Add a "However..." paragraph below |
| Want to clarify a section | Edit the original until it reads correctly without explanation | Add a `Note:` block |
| Decision changed | Replace the old text with the new text | Write "原本是 X，改為 Y" |
| Found a counter-example | Modify the rule to encompass it | Append an "exceptions" list |
| Section feels almost right | Identify the misfit sentence and rewrite it | Add a new paragraph next to it |

**Test:** After editing, would a fresh reader notice this section was revised? If yes, you accreted instead of rewrote.

**When append is legitimate:** Adding genuinely new scope (a new feature, a new error scenario). The addition must still read as a present-tense decided fact, not as a revision of a prior version.

### When Reviewing

Assess against three layers, scoped to what the spec declares:

1. **Intent**: Can I explain why this system exists and for whom?
2. **Design**: Can I predict behavior for any documented user action?
3. **Consistency**: Will similar documented situations be handled similarly?

Key questions:
- Is the documented scope complete enough to implement without guessing?
- Are all design decisions within declared scope explicit?
- Will two implementers produce compatible results for documented behaviors?

Flag: missing layers, vague language, implementation details that should be open, design decisions that should be specified.

**Stay within declared scope** — do not flag missing features outside scope. See spec-quality Review Scope Principle for severity classification.

### Scope Narrowing

When boundaries are unclear or contested during writing or review, help the user **narrow scope** rather than expand it.

| Situation | Do | Don't |
|-----------|-----|-------|
| Unclear boundary (e.g., "what happens at MAX_STEP?") | Ask user to pick a specific value or behavior, then document it | Suggest covering all possible values |
| Feature has many edge cases | Ask which cases matter for the target state, defer the rest to Non-goals | List all edge cases and suggest handling each |
| User unsure about a design choice | Present 2-3 options with tradeoffs, ask user to choose one | Add all options as "configurable" |
| Spec growing large | Suggest deferring non-core features to separate specs | Add more sections to cover everything |

**Principle:** A smaller, well-defined spec is more valuable than a comprehensive but vague one. Help the user decide what is NOT in scope as quickly as possible.

### Bloat Detection

During writing or reviewing, proactively check whether the specification is growing beyond a manageable size. Do not wait for the user to ask about splitting.

| Signal | Threshold | Action |
|--------|-----------|--------|
| Total spec length | Exceeds ~120 lines | Suggest switching to table-of-contents mode |
| Number of features | More than 4 independent features | Suggest extracting feature detail documents |
| Single section length | Any section exceeds ~30 lines | Suggest extracting that section |
| Behavior tables dominate | More than half the spec is behavior tables | Suggest extracting behaviors to detail documents |

When a threshold is reached, inform the user that the spec is growing large and recommend splitting using the Splitting Content rules below. Present the recommendation with the specific signals detected, then proceed with splitting if the user agrees.

### Splitting Content

**Default: Keep everything in SPEC.md.**

As features accumulate, SPEC.md may shift role:

| SPEC.md Role | Signal |
|---|---|
| Full specification | Single feature or few tightly related features |
| Table of contents | Multiple features, each with dedicated detail documents |

In table-of-contents mode, SPEC.md retains decisions and summaries; feature detail documents hold the Behavior and Refinement content.

Use this decision table to determine when to extract content:

| Decides | Expands | External | → Action |
|---------|---------|----------|----------|
| Y | - | - | Keep in SPEC.md |
| N | N | - | Keep in SPEC.md |
| N | Y | N | May extract (summary + link) |
| N | Y | Y | Extract (link only) |

**Conditions:**
- **Decides**: Cannot understand what to build without reading this
- **Expands**: Complete definition of a decision (all fields, all cases)
- **External**: Maintained by different role/tool

**When "May extract" becomes "Should extract":**
- Content length makes SPEC.md hard to scan (rough signal: section exceeds ~30 lines)
- Same content is referenced from multiple features
- SPEC.md is in table-of-contents mode

**Examples:**

| Content | Decides | Expands | External | → Action |
|---------|---------|---------|----------|----------|
| Feature behavior | Y | - | - | Keep |
| Decision table | Y | - | - | Keep |
| Error handling rules | Y | - | - | Keep |
| API endpoints (3-5) | N | N | - | Keep |
| Full DB schema (50+ fields) | N | Y | N | May extract |
| Complete test cases | N | Y | N | May extract |
| Figma design | N | Y | Y | Extract |

**When extracting:**
- **Extract = move + summarize, not delete.** SPEC.md keeps the governing decision and a one-line summary.
- Link: `See [Schema](docs/schema.md) for field definitions`
- Detail documents follow the same decision table above

| Type | Location |
|------|----------|
| Data structures | `docs/schema.md` |
| Visual design | `docs/design.md` or external |
| Test cases | `docs/tests.md` |

**When updating specifications:**
- First determine: decision or detail?
- Decisions go in SPEC.md, details may go in referenced documents
- If adding to external document, verify SPEC.md has the governing decision

**Writing tip:** Use tables (like this decision table) to define boundaries and rules. Tables make conditions explicit and reduce ambiguity.

### Handling Uncertainty

#### Undecided design choices

Do not leave gaps. Instead:

1. Present options with tradeoffs
2. Request a decision
3. Document the choice

#### Incomplete information

Mark explicitly what is decided vs pending:

```
## Technical Stack

Decided:
- Runtime: Node.js >= 20

To be decided:
- Database: PostgreSQL or SQLite
  (depends on deployment target)
```

A technology under "Decided" must carry a binding constraint—a property the target state requires, a cross-implementer convention, or an external mandate. If the choice is incidental (any equivalent would do), rewrite it as the property and leave the technology open rather than recording an arbitrary pick. See spec-principles Technology Choices for the discriminator test.

Resolve all "To be decided" items within the target scope before implementation readiness—an implementer should be able to build without clarifying questions. Items outside the current scope may remain deferred with explicit owners.

#### Conflicting requirements

Surface the conflict explicitly and request resolution rather than making assumptions.

## Completion Rubric

### Before Applying Methodology

| Criterion | Pass | Fail |
|-----------|------|------|
| Starting phase identified | Know which phase to begin from | Phase not determined |
| Existing content assessed | Evaluated current spec against rubric | No assessment of current state |
| Target quality level known | Minimal/Usable/Complete selected | No target level |

### During Application

| Criterion | Pass | Fail |
|-----------|------|------|
| Phase discipline | Completing current phase before advancing | Skipping phases |
| Gate confirmation | Phase transition criteria checked | Advancing without verification |
| Uncertainty surfaced | Undecided items explicitly marked | Gaps left silently |
| Content placement correct | Decisions in SPEC.md, details in referenced docs | Content misplaced |

### After Application

| Criterion | Pass | Fail |
|-----------|------|------|
| All phases completed | Reached target phase with gates passed | Phases incomplete |
| Quality rubric passed | Target quality level criteria met | Quality level not reached |
| Pending items resolved or deferred | "To be decided" items within target scope resolved; out-of-scope items explicitly deferred | Undecided items within scope remain |
