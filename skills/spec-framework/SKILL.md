---
name: spec-framework
description: Structure specifications using the three-layer framework (Intent, Design, Consistency) with boundary types and implementation standards. Use when organizing spec content into layers or selecting elements.
---

# Specification Framework

Detailed structure for each layer of a specification.

## Applicability Rubric

| Condition | Pass | Fail |
|-----------|------|------|
| Structuring new spec | Writing a spec from scratch | Spec structure already established |
| Layer completeness check | Need to verify all layers are addressed | Layers are already complete |
| Element selection | Deciding which elements to include | Elements already chosen |
| Multi-layer coordination | Ensuring layers align with each other | Single-layer change only |

**Apply when**: Any condition passes

## Core Principles

### Intent Layer

Provides context for judgment calls:

| Element | Role | Core |
|---------|------|------|
| Purpose | What problem the system solves | ✓ |
| Users | Who uses it, what they accomplish | ✓ |
| Impacts | What behavior changes indicate success (drives priority) | |
| Success criteria | What defines "done" and "working" | |
| Non-goals | What the system deliberately is not; building it contradicts Purpose or Users | |
| Out of Scope | What this specification does not cover, though it is compatible with intent | |

Without intent, implementers make technically correct but misaligned decisions.

**Non-goals vs Out of Scope:** Both describe things that will not be built now, but they instruct the reader differently—a Non-goal tells a contributor to stop, an Out of Scope item tells them to seek a scope decision. Keep them as separate sections so the section heading itself carries the instruction; a category column inside one list gets skimmed past. Each Out of Scope entry states the boundary the target state holds instead; a tracking reference may follow it when the target is confirmed to exist, and never a timeline. See spec-principles Scope Exclusions for the discriminator test.

```
## Non-goals
Building these would contradict what this system is:
- No web UI — this tool composes into shell pipelines

## Out of Scope
Compatible with intent; this specification does not cover them:
- Multi-language UI — every message ships in one language and is never resolved per locale
- Batch import — creation accepts one record per request; see ROADMAP.md#import
```

Work owned by another system belongs to the Design layer's System boundary, not to either section.

### Design Layer

Defines observable behaviors and boundaries:

| Element | Role | Format | Core |
|---------|------|--------|------|
| System boundary | What's inside vs outside | See Boundary types | |
| User journeys | Task flows achieving impacts | Context → Action → Outcome | |
| Interfaces | Contracts between internal modules | - | |
| Presenter | How system presents to users | UI: colors, layout / CLI: output format / API: response structure | |
| Behaviors | Outcomes for each state × operation | State + Operation → Result | ✓ |
| Error scenarios | How failures are handled | - | ✓ |

**Boundary types:**

| Type | Defines | Example |
|------|---------|---------|
| Responsibility | What system does / does not do | "Validates input; does not store history" |
| Interaction | Input assumptions / Output guarantees | "Assumes authenticated user; Returns JSON only" |
| Control | What system controls / depends on | "Controls order state; Depends on payment service" |

### Consistency Layer

Establishes patterns for uniform implementation (all items enhance quality, none required for minimal spec):

| Concept | Role | Example |
|---------|------|---------|
| Context | Shared understanding | "This is event-driven" |
| Terminology | Same concept uses same name throughout | "Order" not "Purchase/Transaction/Request" |
| Pattern | Recurring situation → approach | "State changes via events" |
| Form | Expected structure | "Events have type, payload", "Primary: #FF0000", "Errors to stderr" |
| Contract | Interaction agreement | "Handlers must be idempotent" |

Weave these into relevant sections rather than listing separately.

### Implementation Standards (Optional)

For projects requiring code-level consistency across multiple contributors or extended development periods.

| Category | Purpose | Examples |
|----------|---------|----------|
| Architecture | Module boundaries and dependencies | Clean Architecture, Hexagonal, Layered |
| Design Patterns | Reusable solutions | Repository, Factory, Strategy |
| Testing Style | Test structure and conventions | Given-When-Then, Arrange-Act-Assert |
| Code Organization | Directory structure and naming | Feature-based, layer-based, naming conventions |

**When to include:**
- Multiple contributors work on the codebase
- Development spans extended periods
- Codebase requires shared conventions

**When to skip:**
- Simple scripts or single-file utilities
- Prototypes or proof-of-concept
- Short-lived projects

### Layer Selection Guide

| Project Characteristic | Intent | Design | Consistency | Implementation Standards |
|-----------------------|--------|--------|-------------|-------------------------|
| Solo prototype | Core only | Core only | Skip | Skip |
| Small team (2-5) | Full | Full | Terminology + Patterns | Optional |
| Multi-team | Full | Full | Full | Recommended |
| External API / Public contract | Full | Full | Full | Required |

### Element Completeness Guide

| Design Element | When to Include | When to Skip |
|----------------|-----------------|--------------|
| System boundary | External dependencies exist | Self-contained utility |
| User journeys | Multiple user-facing flows | Single-purpose tool |
| Interfaces | Multiple internal modules | Monolithic implementation |
| Presenter | User-facing output matters | Internal service |
| Behaviors | Always (Core) | Never skip |
| Error scenarios | Always (Core) | Never skip |

## Completion Rubric

### Before Applying Framework

| Criterion | Pass | Fail |
|-----------|------|------|
| Scope understanding | Know what the system does | Scope unclear |
| Layer necessity evaluated | Determined which layers needed per project type | Blindly applying all layers |
| Existing content assessed | Reviewed any existing spec content | Starting without context |

### During Application

| Criterion | Pass | Fail |
|-----------|------|------|
| Intent before Design | Intent layer completed before Design details | Design written without Intent |
| Core elements present | All Core-marked elements addressed | Core elements missing |
| Boundaries explicit | System boundaries clearly defined | Boundaries implied or missing |
| Layer selection justified | Layer depth matches project characteristic | Over/under-layered for context |

### After Application

| Criterion | Pass | Fail |
|-----------|------|------|
| Intra-layer consistency | Elements within each layer do not contradict | Internal contradictions |
| Cross-layer alignment | Design serves Intent; Consistency supports Design | Layers misaligned |
| Element completeness | Included elements fully specified | Partial elements left incomplete |
