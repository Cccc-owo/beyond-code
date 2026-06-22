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
understand the codebase quickly.

# Modes

**Scan** — triggered when the user says "study this project" or
similar. Do a deep scan and generate fresh docs. If `.beyond-code/
.project/` already exists, warn the user before overwriting and ask
whether to regenerate from scratch or update only what changed.

**Maintain** — triggered after an initiative is archived, when
`.beyond-code/.project/` already exists. Ask the user which areas
changed (architecture, conventions, entry points, etc.) and update
only the corresponding files. If the user is unsure, offer to review
the changes and suggest which docs need updating. Do not regenerate
all five files from scratch.

Each mode is described in detail below.

## Scan mode

When the user asks you to study the project, generate documentation
under `.beyond-code/.project/`. Only do this on explicit request —
scanning a project deeply is expensive.

### Files to generate

**index.md** — the entry point for any agent. Describe what the
project does, what it aims to achieve, and how the top-level
directories map to responsibilities. Do not list naming conventions
here — those go in conventions.md.

**architecture.md** — for each major module or directory, describe
its responsibility, what it depends on, and any notable design
decisions. Write for someone who needs to understand the codebase
at a structural level.

**call-chains.md** — end-to-end code paths: for each critical user
action, trace the sequence of function/method calls from entry
point to result.

**conventions.md** — naming patterns, code formatting rules, commit
conventions, and lint/type-check configuration the project enforces.

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
generated: <today's date>
commit: abc1234 | none
---
```

Read the project: start with README and package metadata, then the
top-level directory structure, then trace imports from entry points
outward to understand module boundaries and data flow. Limit the
deep scan to 20 key source files, prioritized by import count. If
more depth is needed, ask the user.

"Trivially small" means ≤3 source files and no subdirectories. In
that case, generate only index.md. Otherwise, generate all 5 files.
Skip the scan entirely if the user says not to. If the project has
an existing doc convention (such as a docs/ directory), follow it
instead.

### Staleness detection

When reading these files later, compare the recorded commit or date
against the current state. If they differ, warn the user the docs
may be stale.

## Maintain mode

This mode is triggered from the verify stage after archive. If
`.beyond-code/.project/` exists, ask the user:

> "I learned a few things during this initiative. Should I update
> any project docs?"

If the user says yes, ask which areas changed (architecture,
conventions, entry points, call chains, or project overview).
Update only the corresponding files. Do not regenerate all five
files from scratch — only update what changed. If the user is
unsure, offer to review the changes and suggest which docs need
updating. Update the generation metadata (date and commit) for
each modified file.
