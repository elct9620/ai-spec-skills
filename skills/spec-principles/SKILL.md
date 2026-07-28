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

### Scope Exclusions

A specification excludes far more than it includes. Readers act on exclusions during development, so an exclusion that does not say **why it is excluded** gets read as a standing refusal—and work that was merely outside this target state gets rejected as if it violated intent.

Every exclusion falls into one of three categories, distinguished by **what would have to change for it to become included**:

| Category | What it declares | Reversal requires | Where it lives |
|----------|------------------|-------------------|----------------|
| Non-goal | The system deliberately is not that; building it contradicts Purpose or Users | A new intent decision | Intent layer — `Non-goals` |
| Out of scope | Compatible with intent, simply not part of this target state | A scope decision (spec revision); intent unchanged | Intent layer — `Out of Scope` |
| Delegated | Another system, spec, or team owns it | Nothing—it already exists elsewhere | Design layer — System boundary |

**Discriminator test:** *If a contributor implemented this, what would break?*

- Intent would be violated → **Non-goal**. A contributor should stop; changing this reopens Purpose.
- Nothing would be violated, but the spec would no longer describe the system → **Out of scope**. A contributor should request a scope decision, not abandon the idea.
- Nothing—it duplicates something that exists → **Delegated**. Point at the owner.

**These categories are not a schedule.** "Out of scope" says *this specification does not cover it*, never *we will build it in v2*. A timeline turns the spec back into a plan (see Anti-Patterns: Phase / pending markers).

**Each Out of Scope entry states the boundary the target state holds instead**—what the system does in place of the excluded capability. That statement is always available, because it is a fact about the spec being written; it is verifiable without leaving the document; and it constrains implementation. "Flag values are service-wide and the read path carries no user identity" tells an implementer something. A bare "per-user targeting" tells them nothing.

A pointer to where the decision is tracked—a roadmap entry, an issue, another spec—may follow the boundary statement, but only after confirming the target exists. Never write a path, anchor, or issue number you have not checked: an invented reference reads as authoritative and sends the next reader somewhere that is not there. When nothing trackable exists, the boundary statement alone is a complete entry. Keeping the reference optional is what removes the incentive to invent one—it never decides whether the entry is well-formed.

| Well-formed | Ill-formed | Why |
|-------------|-----------|-----|
| `Non-goals: No web UI — this tool composes into pipelines` | `Non-goals: Web UI` | A bare item cannot be told apart from a deferral; the reason is what makes it binding |
| `Out of Scope: Multi-language UI — every message ships in one language and is never resolved per locale` | `Non-goals: Multi-language UI` | Compatible with intent; filing it under Non-goals reads as a refusal |
| `Out of Scope: Batch import — creation accepts one record per request; see ROADMAP.md#import` | `Out of Scope: Batch import — see ROADMAP.md#import` | Where the boundary is missing, an unverified link is all that remains—and if the path is wrong, the entry says nothing at all |
| `Out of Scope: Offline editing — edits require an open connection and are rejected otherwise` | `Offline editing (Future)` | A boundary states what is true now; `(Future)` states when something else becomes true |
| `System boundary: Authentication is performed by the platform gateway` | `Non-goals: Authentication` | The system depends on it; it is a boundary, not a refusal |

An exclusion that states neither the intent it would contradict (Non-goal) nor the boundary held instead (Out of Scope) carries no decision—collapse it into whichever category the discriminator test selects, or drop it.

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
| Phase / pending markers | `(Future)`, `(v2)`, `(暫定)`, `TBD`, `TODO` in spec body | Spec describes a decided target state; pending marks leave doubt about whether decisions are final | Decide now and write the state; if it is simply not covered, move it to `Out of Scope` with a tracking reference (see Scope Exclusions); for genuinely undecided items use Handling Uncertainty in spec-methodology |
| Optional markers | `(Optional)` in spec | Either required for target state or not | Make it required, or move it to the exclusion category the discriminator test selects (see Scope Exclusions) |
| Conflated exclusions | Everything not built sits under `Non-goals`; entries carry no reason or reference | Readers cannot tell a standing refusal from an item this spec merely does not cover, so both get treated as refusals | Split by the discriminator test: intent violation → Non-goals; scope edge → Out of Scope; owned elsewhere → System boundary |
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
| Scope definition | Clear what is included and excluded, and each exclusion's category is known | Scope is open-ended; exclusions lumped together |

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
