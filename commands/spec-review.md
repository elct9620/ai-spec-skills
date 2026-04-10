---
name: spec-review
description: Review specifications against quality criteria and three-layer framework.
argument-hint: "[path/to/SPEC.md]"
allowed-tools: Read, Grep, Glob, Bash(git status:*), Bash(git log:*), Bash(git diff:*), Skill(spec:spec-principles), Skill(spec:spec-framework), Skill(spec:spec-quality), Skill(spec:spec-methodology)
---

## Rule

The `<execute name="main">ARGUMENTS</execute>` is entry point for this command.

## Skills Rubric

To select skills for reviewing specifications, consider the following rubric:

| Skill                    | When to use                                                                        | Core |
|--------------------------|------------------------------------------------------------------------------------|------|
| `spec:spec-principles`   | Always — foundational knowledge for any specification work.                        | ✓    |
| `spec:spec-framework`    | Assessing whether specification layers are complete and well-structured.            |      |
| `spec:spec-quality`      | Running the rubric, balance check, and identifying common problems.                | ✓    |
| `spec:spec-methodology`  | Applying review assessment method and suggesting improvements.                     |      |

## Definition

<function name="read-spec">
    <description>Read and parse the specification file to review.</description>
    <parameter name="path" type="string" description="Path to the specification file. Defaults to SPEC.md if not provided." required="false"/>
    <step>1. locate the specification file (use provided path or search for SPEC.md)</step>
    <step>2. read the full content of the specification</step>
    <step>3. identify the structure and sections present</step>
    <step>4. scan SPEC.md for document links matching pattern [text](docs/...) or relative .md paths</step>
    <step>5. for each linked document: record the link and attempt to read the file if it exists</step>
    <step>6. identify declared scope: what features and boundaries the spec claims to cover, and any explicit non-goals</step>
    <return>parsed specification content, structure overview, declared scope summary, and linked documents map (path → content or "missing")</return>
</function>

<function name="determine-target">
    <description>Determine the user's target quality level to scope the review appropriately.</description>
    <parameter name="spec" type="string" description="The parsed specification content." required="true"/>
    <step>1. check if the user specified a target quality level</step>
    <step>2. if not specified, infer from project context (team size, spec maturity, stated goals)</step>
    <step>3. default to Minimal if context is insufficient</step>
    <step>4. confirm: this review will evaluate against [level] criteria</step>
    <return>target quality level (Minimal/Usable/Complete) and which rubric items are in scope</return>
</function>

<function name="active-skills">
    <description>According to the review requirements, determine which skills are needed and activate them.</description>
    <parameter name="context" type="string" description="The specification content and structure overview." required="true"/>
    <step>1. always activate core skills: spec:spec-principles, spec:spec-quality</step>
    <step>2. discover available skills from system-reminder</step>
    <step>3. analyze the context with rubric of available skills</step>
    <step>4. select additional skills that are most relevant to the review</step>
    <loop for="skill in $selected-skills">
        <step>5. use Skill($skill) to activate and load its knowledge</step>
    </loop>
    <return>list of activated skills with their knowledge loaded (core skills always included)</return>
</function>

<function name="assess-layers">
    <description>Evaluate the specification against the three-layer framework (Intent, Design, Consistency), scoped to declared content.</description>
    <parameter name="spec" type="string" description="The parsed specification content." required="true"/>
    <step>1. assess Intent layer: Can I explain why this system exists and for whom?</step>
    <step>2. assess Design layer: Can I predict behavior for any documented user action?</step>
    <step>3. assess Consistency layer: Will similar documented situations be handled similarly?</step>
    <step>4. note missing layers, weak areas, and strengths within declared scope</step>
    <return>three-layer assessment with findings per layer</return>
</function>

<function name="apply-rubric">
    <description>Execute the Specification Rubric with 11 Y/N criteria.</description>
    <parameter name="spec" type="string" description="The parsed specification content." required="true"/>
    <step>1. evaluate Intent Layer criteria (#1-4): Purpose, Users, Impacts, Success criteria</step>
    <step>2. evaluate Design Layer criteria (#5-8): Behaviors, Error scenarios, Contracts, Implementability</step>
    <step>3. evaluate Consistency Layer criteria (#9-11): Terminology, Patterns, Compatibility</step>
    <step>4. determine passing status based on Required items</step>
    <step>5. classify quality level per Quality Level Classification (Minimal/Usable/Complete/Over-specified)</step>
    <return>rubric scorecard with Y/N for each criterion, passing status, and quality level</return>
</function>

<function name="check-balance">
    <description>Check for over-specification and under-specification.</description>
    <parameter name="spec" type="string" description="The parsed specification content." required="true"/>
    <step>1. scan for implementation details that should be left open (over-specification)</step>
    <step>2. scan for vague terms like "appropriate", "reasonable" (under-specification)</step>
    <step>3. scan for undefined behavior for reachable states within documented features</step>
    <step>4. apply the test: "If implemented differently, would users notice or would modules conflict?"</step>
    <return>balance assessment with specific findings</return>
</function>

<function name="check-structure">
    <description>Verify structural integrity when SPEC.md links to extracted detail documents.</description>
    <parameter name="spec" type="string" description="The parsed SPEC.md content." required="true"/>
    <parameter name="linked-docs" type="map" description="Map of linked document paths to their content or 'missing'." required="true"/>
    <step>1. identify all outbound links in SPEC.md (pattern: [text](path))</step>
    <step>2. for each link: check whether the target file exists (flag broken links)</step>
    <step>3. for each extracted section: verify SPEC.md retains a governing decision or summary sentence above the link (flag link-only extractions)</step>
    <step>4. for each readable linked document: do a light consistency check — does the detail document contradict any decision stated in SPEC.md?</step>
    <step>5. determine SPEC.md mode: full-specification or table-of-contents (based on link density and structure)</step>
    <return>structure report: broken links, link-only extractions (missing governing decision), consistency conflicts, and detected SPEC.md mode</return>
</function>

<function name="identify-problems">
    <description>Check against the Common Problems table, classifying each finding by severity.</description>
    <parameter name="spec" type="string" description="The parsed specification content." required="true"/>
    <parameter name="declared-scope" type="string" description="The spec's declared scope from read-spec." required="true"/>
    <parameter name="target-level" type="string" description="Target quality level from determine-target." required="true"/>
    <step>1. check for Must Fix problems within declared scope: Missing intent, Over-specification, Vague language, Explanatory notes, Phase markers</step>
    <step>2. check for Suggested Fix problems: Undefined scenarios that would cause crash/data-loss/security-breach for documented features, Hidden assumptions</step>
    <step>3. check for Note-level items only if target level requires them: Inconsistent patterns, Inconsistent terminology, Undefined scenarios (detail-level)</step>
    <step>4. for each finding: cite the specific text in the spec that evidences the problem</step>
    <step>5. do NOT flag missing content that is outside the spec's declared scope</step>
    <return>list of identified problems classified by severity (Must Fix / Suggested Fix / Note) with evidence citations</return>
</function>

<function name="generate-report">
    <description>Generate the final review report organized by severity, respecting declared scope.</description>
    <parameter name="layer-assessment" type="string" description="Three-layer assessment results." required="true"/>
    <parameter name="rubric-scores" type="string" description="Rubric scorecard results." required="true"/>
    <parameter name="balance-check" type="string" description="Balance assessment results." required="true"/>
    <parameter name="structure-check" type="string" description="Structure integrity check results." required="true"/>
    <parameter name="problems" type="list" description="Identified problems classified by severity." required="true"/>
    <parameter name="target-level" type="string" description="Target quality level." required="true"/>
    <step>1. compile rubric scores into a summary table, marking which items are required for the target level</step>
    <step>2. report Must Fix items: defects that must be resolved (Required rubric N, anti-patterns, vague language within scope)</step>
    <step>3. report Suggested Fix items: stability/security gaps in documented features</step>
    <step>4. report Notes: non-required items that would improve quality if user targets a higher level — frame as "To reach [level]: ..."</step>
    <step>5. include structure findings: broken links and link-only extractions as Must Fix</step>
    <step>6. determine current quality level and whether it meets the target</step>
    <step>7. do NOT suggest adding new features, expanding scope, or ask "do you want to add X?"</step>
    <return>review report organized by severity (Must Fix → Suggested Fix → Notes) with current quality level and target assessment</return>
</function>

<procedure name="main">
    <parameter name="path" type="string" description="Path to the specification file to review." required="false"/>
    <step>1. <execute name="read-spec" path="$path"/></step>
    <step>2. <execute name="determine-target" spec="$spec"/></step>
    <step>3. <execute name="active-skills" context="$spec"/></step>
    <step>4. <execute name="assess-layers" spec="$spec"/></step>
    <step>5. <execute name="apply-rubric" spec="$spec"/></step>
    <step>6. <execute name="check-balance" spec="$spec"/></step>
    <step>7. <execute name="check-structure" spec="$spec" linked-docs="$linked-docs"/></step>
    <step>8. <execute name="identify-problems" spec="$spec" declared-scope="$declared-scope" target-level="$target-level"/></step>
    <step>9. <execute name="generate-report" layer-assessment="$layer-assessment" rubric-scores="$rubric-scores" balance-check="$balance-check" structure-check="$structure-check" problems="$problems" target-level="$target-level"/></step>
    <return>specification review report with severity-classified findings</return>
</procedure>

## Task

<execute name="main">$ARGUMENTS</execute>
