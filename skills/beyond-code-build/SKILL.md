---
name: beyond-code-build
description: >
  Use when executing a confirmed task list, building features task
  by task, tracking progress through status.md, or entering the
  build phase of beyond-code. Covers task execution loop, status
  synchronization, gap tracking, and error handling.
---

# Purpose

Your task is to execute the task list from `tasks.md`, one task at
a time. Follow the plan's order, stay in scope, and keep status.md
updated so progress is always visible.

Read `tasks.md`. Follow the task order. Mark each one `- [x]` when
done.

Read `.beyond-code/config.yaml` for commit preferences (set by the
beyond-code router — do not create or modify this file). Commits: if
set to per-task, commit after each completed task. If set to per-plan,
commit once after all tasks. If set to manual, do not commit on your
own. Match the format from config (project convention, conventional
style, or user-written).

Before starting each task that has `depends_on` in `tasks.md`, check
that initiative's status.md — it must have reached `stage: verify`
with `gate: none`.
If tasks declare a dependency, wait for the prerequisite to complete.
If two tasks are independent, you may run them in parallel.

After each task, mark the checkbox in `tasks.md`. Every 3 tasks, or
before handing control back to the user, also update:
- `status.md` field `next` to the next task description
- `status.md` field `last_completed` to the task just finished

When committing (per-task or per-plan), include the task description
in the commit message.

Follow the existing patterns in each file. When you encounter a file
not in the task list:
- If the tasks cannot be completed without changing it, pause and
  inform the user. Do not expand scope unilaterally. Update
  `tasks.md` only after the user approves.
- If it is an improvement that can wait, note it under `## Gaps`.

If something comes up that the task list did not cover, add it to the
`## Gaps` section of `tasks.md`. For each gap, record:
`- [ ] <what was discovered> — <why out of scope> — <suggested approach>`.
Do not expand scope within the current initiative. Address gaps as a
separate initiative once the current one is complete.

If your platform supports delegating work to a sub-agent (e.g. via a
Task tool), a task is a candidate when it is self-contained — someone
can complete it by reading only the task description, the relevant
files, and this skill. If a task is tightly coupled to decisions being
made in other tasks, keep it in the main session. When delegating,
include exactly what the sub-agent needs: the task, the file paths,
the relevant design context, and the path to the initiative directory
so it can read and update the tasks and status files.

If a task fails, try up to 3 obvious fixes: correct a typo, fix a
wrong path or import, adjust an assumption that proved incorrect,
rerun after a transient failure. If none succeed, or a fix requires
changing the approach or touching code outside the task list, stop
and report what you tried. Do not loop silently.

If a failing task blocks tasks that depend on it, report which
subsequent tasks are now blocked and ask the user whether to pause
the initiative, re-plan, or skip the blocking task and proceed
with independent work.

When all tasks are done, update status.md:
set `stage: verify`, `gate: none`, `next: run automated checks`,
`last_completed: all tasks`.

Then present a completion summary to the user. State what was done,
not that it was done — list the files changed, the rough scope of
each change, and any notable observations that came up during
implementation. Example:

> All 6 tasks complete. I changed 3 files:
> - `src/auth/login.ts` — added JWT verification middleware
> - `src/auth/types.ts` — added TokenPayload interface
> - `tests/login.test.ts` — 4 new test cases for token expiry
>
> One thing worth noting: the existing session store uses in-memory
> storage. It works for now but won't survive restarts. I recorded
> this under Gaps.

Load `beyond-code-verify` to proceed.
