---
name: beyond-code
description: >
  Use when the user wants to think through requirements before coding,
  asks to design or plan first before implementing, or uses /beyond-code.
  Also when the task spans architectural decisions or the user's intent
  is unclear. Skip when the change is straightforward with no design
  decisions to make.
---

# Role

Your default posture is to ask before assuming and confirm before
acting. When the user's intent is ambiguous, clarify instead of
guessing. When the user wants to move fast, accommodate — skip the
parts they say to skip. When they want to be thorough, offer more
questions and more detail. The user decides the pace; you provide
the structure.

Before any code is written, you must understand what the user wants,
propose a concrete approach, and get explicit confirmation. After
implementation, you must verify through automated checks and then
walk the user through the result for acceptance.

Never declare work complete on your own. Never skip a confirmation
gate unless the user explicitly tells you to.

# When the request is unclear

Update `.beyond-code/<slug>/status.md`:
set `stage: think`, `gate: none`, `next: clarify requirements with user`.

Load the `beyond-code-think` skill to proceed.

# Before implementing

Update `.beyond-code/<slug>/status.md`:
set `stage: plan`, `gate: none`, `next: create design.md and tasks.md`.

Load the `beyond-code-plan` skill to proceed.

# While implementing

Update `.beyond-code/<slug>/status.md`:
set `stage: build`, `gate: none`, `next: <first task description>`.

Load the `beyond-code-build` skill to proceed.

# After implementing

Update `.beyond-code/<slug>/status.md`:
set `stage: verify`, `gate: none`, `next: run automated checks`.

Load the `beyond-code-verify` skill to proceed.

# Managing multiple pieces of work

Each distinct goal gets its own directory under `.beyond-code/`.

If one depends on another, declare it in `tasks.md` frontmatter with
`depends_on`. When listing active initiatives, show each slug with its
stage and whether it is blocked by an unfinished dependency. Completed
work lives under `.beyond-code/.archive/`.

A request that involves a single function, a well-understood bug fix
in one file, or where the user says "just fix it" should be handled
directly without using this flow.

If the user does not specify an initiative, list the active ones with
stage and dependency status. If the conversation already references
one, ask whether to continue that one. Otherwise let the user pick.

If a slug already exists, do not overwrite. Try a variant or ask the
user for a new name.

# Resuming after interruption

When re-entering a session, check `.beyond-code/` for existing
initiatives (excluding `.archive/`). If more than one is active,
list them and let the user choose. Do not assume.

If you find one, read its `status.md`. It tells you the stage, whether
the agent is waiting on user confirmation (`gate`), and the immediate
next action (`next`). Use `next` to resume — you should not need to
read other files to know what to do.

If status.md is missing, unreadable, or has no `next` field,
reconstruct state from the other files: if `design.md` and `tasks.md`
exist but no tasks are checked off, planning is done and build has
not started. If tasks are checked, resume from the first unchecked
one. If you cannot determine the stage, tell the user what you see
and ask.

Then ask the user whether to continue from there or start fresh.
If they want a fresh start, do not delete the old initiative —
move it to `.archive/` or ask the user what to do with it.

# Understanding the project

Load the `beyond-code-project-docs` skill to generate or update
project documentation. Only trigger on explicit request — "study
this project" or similar.

When reading previously generated docs under `.beyond-code/.project/`,
compare the recorded commit or date against the current state. If
they differ, warn the user the docs may be stale.

# Commit preferences

When starting an initiative, check `.beyond-code/config.yaml`. If
absent, ask the user how they want commits handled and write the file.

```
.beyond-code/config.yaml

commit:
  when: per-task | per-plan | manual    # default per-task
  format: project | conventional | user # default project
```

`when` controls commit timing:
- `per-task` — commit after each completed task
- `per-plan` — commit once after all tasks are done
- `manual` — never commit on your own; let the user decide

`format` controls commit message style:
- `project` — follow the repo's existing commit conventions
- `conventional` — use Conventional Commits format (`feat:`, `fix:`, etc.)
- `user` — ask the user for their preferred format and write it here

Read this file at the start of each initiative.
