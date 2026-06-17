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

### Technology Choices

Naming a specific technology, library, or framework is **not automatically over-specification**. The discriminator is whether the choice carries a binding constraint the target state depends on—not whether a technology name appears.

| Category | Example | Treatment | How to write it |
|----------|---------|-----------|-----------------|
| Incidental pick | "Store in PostgreSQL users table" when any equivalent store would do | Over-specification | State the property: "Persist user profile across sessions" |
| Property instance (security) | "Use bcrypt to hash passwords"—the real requirement is a security property | Specify the property, keep the technology open | "Hash passwords with an adaptive function, work factor ≥ N"; bcrypt is one valid choice |
| Property instance (non-security) | "Store money in a `decimal` column"—the real requirement is exactness | Specify the property, keep the technology open | "Represent monetary amounts without rounding error (no binary floating point)"; a decimal type is one valid choice |
| Technology as requirement | "Integrate with the team's PostgreSQL", "Use Stripe", "Run on AWS GovCloud"—the specific technology is mandated by interop, compliance, or external contract | Properly constrained—name it | Name the technology as a binding constraint |

**Discriminator test:** *If an implementer substituted an equivalent technology, would intent be violated?*

- No → incidental; open it (naming it is over-specification)
- Yes, because a property must hold → specify the property; the technology is an example
- Yes, because that exact technology is mandated → specify the technology

When a property has many valid instances, prefer stating the property; collapse to a named technology only when the team standardizes on one for cross-implementer consistency, or when an external contract mandates it. A binding constraint states **what the target state requires** (a property, or a mandated component), not **why one option beat another**—rationale ("bcrypt is safer than MD5") still belongs in an ADR or commit log (see Anti-Patterns: Thinking traces).

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
| Phase / pending markers | `(Future)`, `(v2)`, `(暫定)`, `TBD`, `TODO` in spec body | Spec describes a decided target state; pending marks leave doubt about whether decisions are final | Decide now and write the state; for genuinely undecided items use Handling Uncertainty in spec-methodology, not inline markers |
| Optional markers | `(Optional)` in spec | Either required for target state or not | Move to Non-goals or make it required |
| Thinking traces | "Originally X, changed to Y"; "Considered A, chose B"; inline rationale ("because we chose..."); revision notes | Spec is the current target state, not a record of the journey or deliberation; rationale and history belong in commit log or ADR | Rewrite as the current decided state; remove the trace |
| Over-specification | Algorithm details, or a technology named without a binding constraint (see Technology Choices) | Constrains implementer without design benefit | Replace with observable behavior, or state the binding property the technology must satisfy |
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
