---
name: spec-quality
description: Assess specification quality using 11-item rubric, balance check, and common problems table. Use when reviewing specs before implementation or verifying quality gates.
---

# Specification Quality

Criteria and tools for evaluating specification quality.

## Applicability Rubric

| Condition | Pass | Fail |
|-----------|------|------|
| Spec review requested | Need to assess spec quality | No review needed |
| Pre-implementation check | Spec about to be implemented | Already implemented |
| Quality gate | Spec must meet minimum bar before proceeding | Quality already verified |
| Improvement cycle | Iterating on spec after feedback | No prior version to compare against |

**Apply when**: Any condition passes

## Core Principles

### Specification Rubric

Rate each item Y (yes) or N (no).

**Intent Layer**
| # | Criterion | Required | Y/N |
|---|-----------|----------|-----|
| 1 | Purpose stated in one sentence? | ✓ | |
| 2 | Target users identified? | ✓ | |
| 3 | Impacts (behavior changes) identified? | | |
| 4 | Success criteria measurable or verifiable? | | |

**Design Layer**
| # | Criterion | Required | Y/N |
|---|-----------|----------|-----|
| 5 | Each documented feature has defined behavior? | ✓ | |
| 6 | Error scenarios cover all documented features? | ✓ | |
| 7 | Interaction points (internal and external) have explicit contracts? | | |
| 8 | Implementer can build without clarifying questions? | ✓ | |

**Consistency Layer**
| # | Criterion | Required | Y/N |
|---|-----------|----------|-----|
| 9 | Key terms defined and used consistently throughout? | | |
| 10 | Recurring situations have named patterns? | | |
| 11 | Two implementers would produce compatible results? | | |

**Evidence Criteria for Y/N scoring:**

Score based on the spec's **declared scope** — what the spec claims to cover. Do not score N because the spec omits something outside its declared scope.

| # | Score Y when | Score N when |
|---|-------------|-------------|
| 1 | Spec contains an explicit sentence stating what problem the system solves | No purpose statement, or purpose restates the feature name ("A login system for logging in") |
| 2 | Spec names specific user roles or personas | "Users" mentioned generically without identifying who or what they accomplish |
| 3 | Spec lists observable behavior changes or measurable outcomes | No impacts listed, or only vague goals ("improve performance") |
| 4 | Success criteria use numbers, conditions, or verifiable statements | Unmeasurable criteria ("fast and reliable", "good UX") |
| 5 | Every feature the spec lists has at least one defined behavior (action → result) | Features listed as bullet points without behavior definitions |
| 6 | Each documented feature has at least one error/failure scenario defined | Missing error handling for documented features, or only generic "handle errors appropriately" |
| 7 | Module interaction points described in the spec have explicit input/output contracts | Modules interact without defined interfaces |
| 8 | An implementer could build the documented scope without asking clarifying questions | Ambiguous terms, undefined states, or missing decisions within documented scope |
| 9 | Key domain terms are defined; each concept has exactly one name throughout | Same concept called different names in different sections |
| 10 | Similar situations within the spec reference shared patterns or conventions | Ad-hoc solutions for similar problems in different sections |
| 11 | Two implementers would produce compatible results for all documented behaviors | Interpretation would vary for documented scope |

**Passing Criteria:**
- Any Required N → Must address before implementation.
- All Required Y → Specification meets **Minimal** level. Usable for small teams.
- All Required Y + #3,4 Y → Specification meets **Usable** level. Stop unless improving quality.
- All items Y → Specification meets **Complete** level. Stop.
- Non-required N → Report as upgrade path. Only address if user targets a higher quality level.

### Quality Level Classification

| Level | Criteria | Suitable For |
|-------|----------|-------------|
| Minimal | All Required items Y (#1,2,5,6,8) | Small team, rapid iteration |
| Usable | All Required Y + #3,4 Y | Standard development |
| Complete | All 11 items Y | Multi-team, external consumers |
| Over-specified | All Y but Balance Check fails | Needs trimming of implementation details |

### Balance Check (for review verification; see spec-principles Specify vs Leave Open for writing decisions)

| Question | Design Decision (specify) | Implementation Detail (open) |
|----------|---------------------------|------------------------------|
| User-visible? | Error messages, CLI output | Log format, variable names |
| Affects module contracts? | Interface signatures | Internal functions |
| Could cause inconsistent interpretation? | Error handling pattern, Business rules | Algorithm choice, Performance optimization |

**Test:** "If implemented differently, would users notice or would modules conflict?"

**Warning signs of over-specification:** internal implementation details, algorithm choices (unless user-visible or cross-implementer consistency required)

**Warning signs of under-specification:** vague terms ("appropriate", "reasonable"), undefined behavior for reachable states within documented features

### Review Scope Principle

Review evaluates the quality of what the spec **declares**, not what it **could** declare.

| Finding Type | Definition | Report As |
|---|---|---|
| Defect | Something present in the spec but incorrect, ambiguous, or dangerous | **Must Fix** |
| Stability/Security Gap | Missing handling that would cause system crash, data loss, or security breach within documented features | **Suggested Fix** |
| Enhancement Gap | Additional detail that would improve quality but system works without it | **Note** (only when user targets higher quality level) |

**Rules:**
- Do NOT suggest adding features or scope the spec doesn't claim to cover
- Do NOT flag non-required rubric items as problems unless the user's target quality level requires them
- Do NOT ask "do you want to add X?" for things outside the spec's declared scope
- Report non-required N items as upgrade path: "To reach [Usable/Complete] level, consider: ..."

### Common Problems (diagnosis; see spec-principles Anti-Patterns for prevention)

| Problem | Symptom | Severity | Fix |
|---------|---------|----------|-----|
| Missing intent | Technical tasks instead of user value | Must Fix (Required #1,2) | Add intent layer |
| Undefined scenarios (stability) | Documented feature lacks error handling; failure would crash or corrupt | Suggested Fix | Add error scenarios for documented features |
| Undefined scenarios (detail) | Could add more edge cases but system functions without them | Note | Mention only if user targets higher quality |
| Over-specification | Implementer constrained unnecessarily | Must Fix | Keep only observable behaviors |
| Inconsistent patterns | Similar problems solved differently | Note | Extract and reference patterns |
| Inconsistent terminology | Same concept has multiple names | Note | Define key terms, use consistently |
| Vague language | Ambiguous interpretation within documented scope | Must Fix | Use specific values or criteria |
| Hidden assumptions | Works only in specific context | Suggested Fix | Make all assumptions explicit |
| Explanatory notes | "Note: because..." appears | Must Fix | Rewrite as direct statement |
| Phase markers | "(Future)", "(v2)" in spec | Must Fix | Remove; spec describes target state |

## Completion Rubric

### Before Quality Assessment

| Criterion | Pass | Fail |
|-----------|------|------|
| Full spec read | Entire specification read and understood | Partial or skimmed reading |
| Declared scope identified | Know what the spec claims to cover (features, boundaries, non-goals) | Evaluating against assumed scope |
| Target quality level selected | Chose Minimal/Usable/Complete based on context | No target level determined |

### During Quality Assessment

| Criterion | Pass | Fail |
|-----------|------|------|
| All 11 items evaluated | Every rubric item scored Y or N | Items skipped or unclear |
| Evidence for each N | Each N has specific evidence from spec | N without justification |
| Balance Check completed | Over/under-specification assessed | Balance not checked |
| Common Problems scanned | All 9 problem types checked | Problems check incomplete |

### After Quality Assessment

| Criterion | Pass | Fail |
|-----------|------|------|
| Findings classified by severity | Must Fix, Suggested Fix, and Note separated clearly | Flat unprioritized list |
| Scope respected | No suggestions to add features or scope beyond what spec declares | Recommending additions outside declared scope |
| Actionable recommendations | Each finding has a specific fix suggestion | Vague improvement advice |
| Clear verdict | Quality level determined (Minimal/Usable/Complete/Over-specified) | No clear assessment |
