---
name: spec-write
description: Write or improve specifications using the progressive approach (Intent, Scope, Behavior, Refinement).
argument-hint: topic|feature
allowed-tools: Read, Grep, Glob, Bash(git status:*), Bash(git log:*), Bash(git diff:*), Skill(spec:spec-principles), Skill(spec:spec-framework), Skill(spec:spec-quality), Skill(spec:spec-methodology)
---

## Rule

The `<execute name="main">ARGUMENTS</execute>` is entry point for this command.

## Skills Rubric

To select skills for writing or improving specifications, consider the following rubric:

| Skill                    | When to use                                                                        | Core |
|--------------------------|------------------------------------------------------------------------------------|------|
| `spec:spec-principles`   | Always — foundational knowledge for any specification work.                        | ✓    |
| `spec:spec-framework`    | Structuring specification content into layers with proper elements.                |      |
| `spec:spec-quality`      | Verifying each phase passes rubric criteria and checking balance.                  |      |
| `spec:spec-methodology`  | Applying the progressive approach, splitting content, handling uncertainty.         | ✓    |

## Definition

<function name="gather-context">
    <description>Search for existing SPEC.md and related files to understand the current state of specifications.</description>
    <parameter name="topic" type="string" description="The topic or feature to write a specification for." required="true"/>
    <step>1. search for existing SPEC.md files in the project</step>
    <step>2. search for related documentation, requirements, or specification files</step>
    <step>3. use `git log` to get context of recent specification changes</step>
    <step>4. summarize the current state of specifications related to the topic</step>
    <return>summary of existing specifications and current project context</return>
</function>

<function name="active-skills">
    <description>According to the task requirements, determine which skills are needed and activate them.</description>
    <parameter name="context" type="string" description="The gathered context about existing specifications." required="true"/>
    <step>1. always activate core skills: spec:spec-principles, spec:spec-methodology</step>
    <step>2. discover available skills from system-reminder</step>
    <step>3. analyze the context with rubric of available skills</step>
    <step>4. select additional skills that are most relevant to the task</step>
    <loop for="skill in $selected-skills">
        <step>5. use Skill($skill) to activate and load its knowledge</step>
    </loop>
    <return>list of activated skills with their knowledge loaded (core skills always included)</return>
</function>

<function name="determine-phase">
    <description>Determine which phase of the progressive approach to start from based on the current specification state.</description>
    <parameter name="context" type="string" description="The gathered context about existing specifications." required="true"/>
    <condition if="no existing specification">
        <step>1. start from Phase 1 (Intent)</step>
    </condition>
    <condition if="existing specification found">
        <step>2. evaluate the specification against the rubric to identify gaps</step>
        <step>3. determine the earliest phase with unmet criteria</step>
    </condition>
    <return>starting phase and gap analysis</return>
</function>

<function name="create-plan">
    <description>Create a writing plan for the specification based on the determined phase and gaps.</description>
    <parameter name="starting-phase" type="string" description="The phase to start from." required="true"/>
    <parameter name="context" type="string" description="The gathered context and gap analysis." required="true"/>
    <step>1. outline sections to write or improve for each remaining phase</step>
    <step>2. identify design decisions that need user input</step>
    <step>3. sequence the writing tasks in progressive order</step>
    <return>specification writing plan</return>
</function>

<function name="write-phase">
    <description>Execute a single phase of the progressive approach, writing or improving the specification.</description>
    <parameter name="phase" type="string" description="The phase to execute (Intent, Scope, Behavior, or Refinement)." required="true"/>
    <parameter name="plan" type="string" description="The writing plan for this phase." required="true"/>
    <condition if="$phase == 'Intent'">
        <step>1. write Purpose, Users, and Impacts sections</step>
        <step>2. verify Purpose and Users are stated</step>
    </condition>
    <condition if="$phase == 'Scope'">
        <step>3. write Feature list and User journeys (Context → Action → Outcome)</step>
        <step>4. verify feature list is complete</step>
    </condition>
    <condition if="$phase == 'Behavior'">
        <step>5. write Feature behaviors and Error scenarios</step>
        <step>6. verify Behaviors and Errors are defined and spec is implementable</step>
    </condition>
    <condition if="$phase == 'Refinement'">
        <step>7. write Patterns, Contracts, and Terminology</step>
        <step>8. verify Contracts, Terminology, and Patterns as needed</step>
    </condition>
    <step>9. verify Completion Rubric of each activated skill for this phase</step>
    <return>completed phase with rubric verification result</return>
</function>

<function name="check-bloat">
    <description>Detect whether the specification has grown beyond manageable size and suggest splitting if needed. This check runs automatically — do not wait for the user to ask.</description>
    <parameter name="spec" type="string" description="The written specification content." required="true"/>
    <step>1. count total lines, number of independent features, and longest section length</step>
    <step>2. evaluate against Bloat Detection thresholds from spec-methodology</step>
    <condition if="any threshold exceeded">
        <step>3. inform the user which signals were detected and recommend splitting</step>
        <step>4. apply the Splitting Content decision table (Decides/Expands/External) to determine what stays and what extracts</step>
        <step>5. present the splitting plan and proceed if user agrees</step>
    </condition>
    <condition if="no threshold exceeded">
        <step>6. continue without splitting</step>
    </condition>
    <return>bloat check result and any splitting actions taken</return>
</function>

<function name="quality-report">
    <description>Generate a final quality report with full rubric scoring.</description>
    <parameter name="phase-results" type="list" description="The results from each completed phase." required="true"/>
    <step>1. run the full Specification Rubric (11 items) against the written specification</step>
    <step>2. run the Balance Check for over-specification and under-specification</step>
    <step>3. check against Common Problems table</step>
    <step>4. for each activated skill, verify its Completion Rubric (Before/During/After)</step>
    <step>5. summarize overall specification quality with scores and recommendations</step>
    <return>specification quality report with rubric scores, skill completion verification, balance assessment, and recommendations</return>
</function>

<procedure name="main">
    <parameter name="topic" type="string" description="The topic or feature to write a specification for." required="true"/>
    <step>1. <execute name="gather-context" topic="$topic"/></step>
    <step>2. use ask question tool to confirm scope with the user</step>
    <step>3. <execute name="active-skills" context="$context"/></step>
    <step>4. <execute name="determine-phase" context="$context"/></step>
    <step>5. enter the plan mode</step>
    <step>6. <execute name="create-plan" starting-phase="$starting-phase" context="$context"/></step>
    <step>7. review the plan to avoid over-specification (constrain design, open implementation)</step>
    <condition if="over-specification detected">
        <step>8. refine the plan to remove implementation details</step>
    </condition>
    <step>9. exit plan mode and wait for user confirmation</step>
    <loop for="phase in ['Intent', 'Scope', 'Behavior', 'Refinement']" from="$starting-phase">
        <step>10. <execute name="write-phase" phase="$phase" plan="$plan"/></step>
        <step>11. confirm rubric criteria for the phase pass before proceeding</step>
    </loop>
    <step>12. <execute name="check-bloat" spec="$spec"/></step>
    <step>13. <execute name="quality-report" phase-results="$phase-results"/></step>
    <step>14. ask user if they want to commit the changes</step>
    <return>specification quality report</return>
</procedure>

## Task

<execute name="main">$ARGUMENTS</execute>
