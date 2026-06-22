---
name: beyond-code-project-docs
description: >
  Use when the user asks to study the project, wants project
  documentation generated from a codebase scan, or needs project
  docs updated after an initiative. Covers deep scanning to generate
  index/architecture/call-chains/conventions/entry-points docs,
  plus targeted updates when new knowledge is captured.
---

# Purpose

Generate and maintain project documentation that helps agents
understand the codebase quickly. Two modes:

- **Scan**: triggered when the user says "study this project" or
  similar. Do a deep scan and generate fresh docs.
- **Maintain**: triggered after an initiative is archived, when
  `.beyond-code/.project/` already exists. Ask the user whether
  the initiative revealed anything worth capturing, then update
  only the relevant files.

## Scan mode

When the user asks you to study the project, generate documentation
under `.beyond-code/.project/`. Only do this on explicit request —
scanning a project deeply is expensive.

### Files to generate

```
.beyond-code/.project/
  index.md           Project positioning, design goals, directory structure
  architecture.md    Module responsibilities, dependencies, design decisions
  call-chains.md     Critical code paths
  conventions.md     Naming conventions, lint rules, code style
  entry-points.md    Start / build / test / deploy commands (one-liners)
```

**index.md** — the entry point for any agent. Describe what the
project does, what it aims to achieve, and how the top-level
directories map to responsibilities. Do not list naming conventions
here — those go in conventions.md.

**architecture.md** — for each major module or directory, describe
its responsibility, what it depends on, and any notable design
decisions. Write for someone who needs to understand the codebase
at a structural level.

**call-chains.md** — trace the most important code paths end to end.
For each one, describe the sequence of function calls and data
transformations from entry point to result.

**conventions.md** — naming patterns, lint configuration in use,
code formatting rules, commit conventions. Anything a contributor
needs to match the project's existing style.

**entry-points.md** — the exact commands to start, build, test,
and deploy the project. One command per line, ready to paste.
Example:
```
Start dev server:  npm run dev
Run tests:         npx jest
Build:             npm run build
```

### Generation process

Each file records when and from what state it was generated:

```
---
generated: YYYY-MM-DD
commit: abc1234 | none
---
```

Read the project deeply: start with README and package metadata,
then the top-level directory structure, then trace imports from
entry points outward to understand module boundaries and data flow.
Skip the deep scan if the project is trivially small or the user
says not to. If the project has an existing doc convention (such
as a docs/ directory), follow it instead.

### Staleness detection

When reading these files later, compare the recorded commit or date
against the current state. If they differ, warn the user the docs
may be stale.

## Maintain mode

This mode is triggered from the verify stage after archive. If
`.beyond-code/.project/` exists, ask the user:

> "I learned a few things during this initiative. Should I update
> any project docs?"

If the user says yes, ask which areas are affected and update only
the relevant files. Do not regenerate all five files from scratch —
only update what changed. Update the generation metadata (date and
commit) for each modified file.
