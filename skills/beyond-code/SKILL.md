---
name: beyond-code
description: >
  Use when the user wants to think through requirements before coding,
  asks to design or plan first before implementing, or uses /beyond-code.
  Also when the task spans architectural decisions or the user's intent
  is unclear. The ONLY skip condition: a trivial bug fix in a small-scale
  project (e.g. a single script under ~100 lines) where the user
  explicitly says "skip beyond-code".
---

# Terminology Reference

This skill uses RFC 2119 keywords:

| Keyword | Meaning |
|---------|---------|
| MUST / REQUIRED | Absolute obligation. Violation = process failure. |
| MUST NOT | Absolute prohibition. Violation = process failure. |
| NEVER | Zero-exception prohibition. |
| HARD-GATE | Must pass before next stage. Blocked = STOP. |
| STOP | Cease current action. Await user instruction. |
| ONLY | Exclusive action — no other path permitted. |
| SHOULD / SHOULD NOT | Strong guidance — deviate only with stated reason. |
| MAY / OPTIONAL | Agent discretion. |
| EVIDENCE BEFORE CLAIMS | No success claim without fresh command output. |

# HARD-GATE: No Code Before Spec

You MUST NOT write or modify ANY code, create ANY file, or take ANY
implementation action until spec.md is in `confirmed` status AND
gate.md Gate 1 is cleared.

The ONLY exception: a single-function, single-file bug fix where the
user explicitly says "skip beyond-code".

To write code without completing think and plan stages is to VIOLATE
this skill. Do not do it.

# Role

Your default posture: ask before assuming, confirm before acting.
When intent is ambiguous, clarify instead of guessing.
When the user wants speed, skip what they say to skip.
When they want thoroughness, ask more, detail more.
The user decides the pace; you provide the structure.

# Initiative Directory Layout

Each initiative lives under `.beyond-code/<slug>/`:

```
.beyond-code/<slug>/
  spec.md    What to build + acceptance criteria
  plan.md    How to build it + exhaustive implementation bounds
  gate.md    Progress ledger — single source of truth
```

gate.md is the single canonical source for initiative state.
All files MUST follow this schema — do not redefine fields in sub-skills.

```
---
slug: <initiative-slug>
created: <YYYY-MM-DD>
---

## Gate 1: Spec Confirmed
- [ ] spec.md created: `<path>`
- [ ] User confirmed: `<timestamp>`
- [ ] Gate cleared

## Gate 2: Plan Ready
- [ ] plan.md created: `<path>`
- [ ] Spec coverage self-review: N/N covered
- [ ] Placeholder scan: 0 found
- [ ] User confirmed: `<timestamp>`
- [ ] Gate cleared

## Gate 3: Task Execution

Commit behavior depends on `.beyond-code/config.yaml`:
- `per-task`: each task gets its own commit — hash per row
- `per-plan`: one commit after all tasks — same hash across all rows
- `manual`: agent does NOT commit — rows show `—`

| Task | Commit | Status |
|------|--------|--------|

## Deviations
> Agent MUST log any implementation decision NOT in plan.md.
| Timestamp | Task | Deviation | Rationale |

## Gate 4: Verification
- [ ] Automated checks passed
- [ ] R<N>: user confirmed

## Gaps
- [ ] <description> — <why out of scope> — <suggested approach>

## Archive
- [ ] Moved to .beyond-code/.archive/<slug>/
```

# Stage Transitions

When a sub-skill completes, return to this file. Read gate.md to
determine the current gate, then follow the corresponding section
below. You are responsible for stage transitions — sub-skills ONLY
handle their own stage's work.

# When the request is unclear

Create `.beyond-code/<slug>/gate.md`:
```
---
slug: <slug>
created: <today>
---

## Gate 1: Spec Confirmed
- [ ] spec.md created
- [ ] User confirmed
```

Load the `beyond-code-think` skill.

# After spec confirmed (Gate 1 cleared)

gate.md Gate 1 MUST show spec confirmed.
Load the `beyond-code-plan` skill.

# After plan ready (Gate 2 cleared)

gate.md Gate 2 MUST show plan ready.
Load the `beyond-code-build` skill.

# After all tasks complete (Gate 3 filled)

gate.md Gate 3 MUST show all tasks complete with commit hashes.
Load the `beyond-code-verify` skill.

# Gate Enforcement

NEVER self-approve a gate. Each HARD-GATE clears ONLY when:
- User gives explicit confirmation ("go", "start", "build it") OR
- User gives "just do it" instruction to skip individual confirmations
- The cleared status is recorded in gate.md with a timestamp

If an ambiguous response ("looks good", "sure", "ok") follows a gate
check, ask explicitly: "Shall I proceed to the next stage?"

# Managing multiple pieces of work

Each distinct goal gets its own directory under `.beyond-code/`.

If one depends on another, declare it in plan.md frontmatter with
`depends_on`. When listing active initiatives, show each slug with its
gate status and whether it is blocked by an unfinished dependency.
Completed work lives under `.beyond-code/.archive/`.

When an initiative is archived, check plan.md frontmatter of other
initiatives for `depends_on` references to the archived slug.
If found, inform the user that the blocked initiative may now be
unblocked.

If the user does not specify an initiative, list the active ones
with gate status and dependency status. If the conversation already
references one, ask whether to continue that one. Otherwise let the
user pick.

If a slug already exists, do not overwrite. Try a variant or ask
the user for a new name.

# Resuming after interruption

When re-entering a session, check `.beyond-code/` for existing
initiatives (excluding `.archive/`). If more than one is active,
list them and let the user choose. Do not assume.

If you find one, read its `gate.md`. It tells you which gates are
cleared, which tasks are done (with commit hashes), and whether the
agent is waiting on user confirmation. Use gate.md as the single
source of truth — you SHOULD NOT need to read other files to know
what to do.

If gate.md is missing or unreadable, reconstruct state from
available files: if spec.md exists but plan.md does not, think is
done and plan has not started. If plan.md exists but gate.md has no
Task Execution entries, planning is done and build has not started.
If tasks are recorded with commits, resume from the first task not
recorded. If you cannot determine the stage, tell the user what you
see and ask.

Then ask whether to continue from there or start fresh. If they
want a fresh start, do not delete the old initiative — move it to
`.archive/` or ask the user what to do with it.

# Aborting an initiative

When the user wants to stop, record in gate.md under a new section:
```
## Status: ABORTED
<timestamp> — <reason>
```
Ask whether to archive or leave the initiative in place.

# Understanding the project

Load the `beyond-code-project-docs` skill to generate or update
project documentation. ONLY trigger on explicit request — "study
this project" or similar.

When reading previously generated docs under `.beyond-code/.project/`,
compare the recorded commit or date against the current state.
If they differ, warn the user the docs may be stale.

# Commit preferences

When starting an initiative, check `.beyond-code/config.yaml`.
If absent, ask the user how they want commits handled and write
the file. Do not re-read or re-create this file — sub-skills only
read it.

```
.beyond-code/config.yaml

commit:
  when: per-task | per-plan | manual    # default per-task
  format: project | conventional | user # default project
```

`when` controls commit timing:
- `per-task` — commit after each completed task. gate.md records a unique short hash per task.
- `per-plan` — commit once after all tasks are done. gate.md records the same hash across all tasks.
- `manual` — NEVER commit on your own; let the user decide. gate.md records `—` for each task.

`format` controls commit message style:
- `project` — follow the repo's existing commit conventions
- `conventional` — use Conventional Commits format
- `user` — ask the user for their preferred format and write it here
