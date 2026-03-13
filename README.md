Spec Skills
===

This is a specification management plugin for AI agents. Mainly designed for Claude Code but fit for other LLMs as well.

## Why

Good specifications prevent costly misunderstandings before code is written. This plugin encodes a structured methodology for writing, reviewing, and maintaining specifications — so you get consistent, high-quality specs without memorizing frameworks or checklists.

## Preferences

This plugin is built around a three-layer specification framework:

| Type        | Selection                                    |
| ----------- | -------------------------------------------- |
| Framework   | Three-Layer (Intent, Design, Consistency)    |
| Principles  | IS/IS-NOT, Constrain Design Open Implementation |
| Quality     | 11-Item Rubric, Balance Check                |
| Methodology | Progressive Approach (Intent → Scope → Behavior → Refinement) |

## Structure

This repository is designed as a Claude Code Plugin which contains the following components:

```
|- /spec-skills
    |- commands/          # Workflow shortcuts
       |- spec-write.md   # Write or improve specifications
       |- spec-review.md  # Review specifications against quality criteria
    |- skills/            # Individual skills with specific knowledge
       |- spec-principles/
       |- spec-framework/
       |- spec-quality/
       |- spec-methodology/
```

## Methodology

The command as entry which defines the workflow, then adaptively selects necessary skills to complete the task.

For example, the `spec-write.md` command uses `spec-methodology` for the progressive writing approach and `spec-principles` to detect anti-patterns like over-specification.

## Command Usage Matrix

Choose the appropriate command based on your task:

| Command        | Purpose              | Arguments                  |
|----------------|----------------------|----------------------------|
| `/spec-write`  | Write or improve specs | `spec file path or topic` |
| `/spec-review` | Review spec quality  | `spec file path`           |

### Skills per Command

| Skill                | /spec-write | /spec-review |
|----------------------|:-----------:|:------------:|
| spec:spec-principles |      ✓      |      ✓       |
| spec:spec-framework  |      ✓      |      ✓       |
| spec:spec-quality    |      ✓      |      ✓       |
| spec:spec-methodology|      ✓      |      ✓       |

### When to Use

- **`/spec-write`**: Creating a new specification or improving an existing one
- **`/spec-review`**: Reviewing a specification for quality, structure, and anti-patterns

### Recommended Workflow

```
/spec-write → /spec-review → /spec-write (if needed)
```

After writing or improving a specification (`/spec-write`), run `/spec-review` to check quality. If the review report contains findings, use `/spec-write` to address them.

## Usage

To use this plugin, add to your marketplace or use `--plugin-dir` in Claude Code. Then choose the command which most fits the task.

## License

Apache-2.0 License. See [LICENSE](./LICENSE) for details.
