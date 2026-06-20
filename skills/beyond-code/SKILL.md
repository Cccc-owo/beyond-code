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

Ask one question at a time. Wait for the user's answer before asking
the next. Each question goes deeper based on the previous answer.

Ask questions that narrow down what is unclear. Each question should
build on the last answer, not follow a predefined list. If the user
describes a UI, ask about interactions and edge states. If they
describe a backend, ask about data shape and failure modes. Let the
substance of the request guide what you ask next.

With each question, offer a few reference viewpoints so the user has
a starting point. For example:
- "Option A vs Option B — here are the tradeoffs"
- "Yes or no, and why"

Do not invent scenarios. If an answer is vague, do not fill in the
gaps yourself — ask a follow-up or offer your best interpretation
and ask if it is correct. Encourage the user to ask you questions back.
When the picture is clear, write the summary to a requirements file.
Also create a status file to track progress:

```
.beyond-code/<slug>/requirements.md
---
status: draft | confirmed
---
# Goal
# Target Users
# Functional Requirements
# Non-functional Requirements
# Constraints
```

Write each requirement clearly and concretely enough that someone
else can read it and know what to build. Avoid single-word entries —
expand each point into a sentence or two. For functional requirements,
describe what the user should be able to do, not how it will be
implemented.

```
.beyond-code/<slug>/status.md
---
stage: think
updated: YYYY-MM-DD
---
One-line description of the current state.
```

After writing the files, ask the user to confirm the requirements,
then move to planning. Write requirements with status: draft. Change
to confirmed only after the user agrees the requirements are correct.
Update the status.md description to reflect what has been captured.

The slug is lowercase letters and hyphens, derived from the request.

Before wrapping up, ask the user if they have any remaining questions.
Mention what was not yet discussed — any directions or tradeoffs left
unexplored — so the user can decide whether to continue or move on.

When the user indicates the requirements are right, or says
"let's plan", move on. The user can also say "just do it" to skip to
implementation (still write the plan file, just skip confirmation).

When moving on, tell the user what you understood and what comes next.
For example: "Here's what I heard... Ready to plan?"

# Before implementing

Update status.md stage to plan and set the description to reflect
the current state of planning.

Create an implementation plan. Start with an architecture overview
that describes the change in enough detail that someone familiar with
the codebase can follow the design without reading the task list first.
Include: the key pieces and their responsibilities, how they connect
(which calls which, what data passes between them), what existing
modules are touched and how, any new abstractions or data structures
introduced, and the order of operations for the primary code path.
Write in prose, three to five paragraphs. If a diagram helps, describe
one in text (ASCII or Mermaid). Specific function names and file paths
belong in the task list, not here.

Group the work into components — logical units, each with a distinct
responsibility. A component might be a new module, a class, a set of
related functions, or a UI element. Describe what each does and why
it exists, not its file path. Group related changes; split unrelated
ones.

Files in Scope should list exact file paths where the change is known
in advance. Where a change touches a directory broadly, list the
directory and the kind of files affected. Be specific enough that the
build phase knows what it may and may not touch.

Write a checklist of concrete steps. Each task should be a single,
verifiable action — something that can be completed in one sitting
and checked off with a clear result. Order tasks by natural build
sequence: foundation before upper layers. Name each task starting
with an action verb: "Add the theme context provider" not "Theme
context". If a task is not self-explanatory, expand it with enough
detail that someone else can pick it up and know what to do.

Before presenting the plan, review it at least once. Look for gaps
in logic, missing edge cases, unclear task descriptions, and files
that should be in scope but are not. Revise what you find.

If the task list grows beyond what can be done in a single session
or spans two clearly separate concerns, split it into multiple
initiatives. Declare the dependency with `depends_on` so order
is preserved.

```
.beyond-code/<slug>/plan.md
---
status: draft | confirmed
depends_on: <another-slug> | none
---
# Architecture Overview
# Components
# Files in Scope
# Tasks
- [ ] Step 1 — concrete, verifiable action
- [ ] Step 2 — concrete, verifiable action (depends on: Step 1)
## Gaps
<!-- Issues discovered during implementation that are out of
     scope for this initiative. Address as a separate initiative. -->
```

Present the plan and give the user a choice:
- Go through each section in detail, one by one
- Review the overview and a summary of each section

Then stop. Summarize the key points — what will change, in what order —
so the user knows what they are confirming.

Confirmation means: "OK", "go ahead", "confirmed", "start", "just do it".

Not confirmation: "looks good", "makes sense", "seems fine", "alright",
"sure", "ok", any reply that sounds like discussion or tentative
agreement. When you see these, do not proceed. Ask again explicitly:
"Shall I start implementing?"

If they say "just do it", proceed but still write the plan file first.

If the user requests changes, revise the plan and present again.
If this happens repeatedly without convergence, offer to capture
the open questions and proceed with what has been agreed so far.

# While implementing

Update status.md stage to build.

Check `.beyond-code/config.yaml` for commit preferences.
If no config exists, ask the user and write one.

Commits: if set to per-task, commit after each completed task. If set
to per-plan, commit once after all tasks. If set to manual, do not
commit on your own. Match the format from config (project convention,
conventional style, or user-written).

Follow the task order. Mark each one `- [x]` when done. Keep the plan
file updated so progress is visible.

If tasks declare a dependency, wait for the prerequisite to complete.
If two tasks are independent, you may run them in parallel.

After each task, update both the checkbox in plan.md and the
description in status.md so a fresh session knows exactly what
was happening. When committing (per-task or per-plan), include
the task description in the commit message.

Follow the existing patterns in each file. When you encounter a file
not in the plan:
- If the plan cannot be completed without changing it, update the plan
  first, then proceed.
- If it is an improvement that can wait, note it under `## Gaps`.

If something comes up that the plan did not cover, add it to the
`## Gaps` section. Do not expand scope within the current initiative.
Address gaps as a separate initiative once the current one is complete.

A task is a candidate for sub-agent delegation when it is
self-contained — someone can complete it by reading only the task
description, the relevant files, and this skill. If a task is
tightly coupled to decisions being made in other tasks, keep it in
the main session. When delegating, include exactly what the
sub-agent needs: the task, the file paths, the relevant plan
context, and the path to the initiative directory so it can read
and update the plan and status files.

If a task fails, try the obvious fixes first: correct a typo, fix a
wrong path or import, adjust an assumption that proved incorrect,
rerun after a transient failure. If a fix requires changing the
approach or touching code outside the plan, it is not obvious —
stop and report what you tried. Do not loop silently.

If a failing task blocks tasks that depend on it, report which
subsequent tasks are now blocked and ask the user whether to pause
the initiative, re-plan, or skip the blocking task and proceed
with independent work.

When all tasks are done, tell the user what was built and ask if
they are ready to verify.

# After implementing

Update status.md stage to verify and set the description to reflect
the current state of verification.

Run the project's own checks. Detect what tools are available:
a linter (eslint, ruff, biome), a type checker (tsc, mypy),
a build step (npm run build, make), and the test suite (jest,
pytest, go test). Use the exact commands the project already
defines — package.json scripts, Makefile targets, or project
config files. Run them in order: lint first, then typecheck,
then build, then tests.

When checks fail, find and fix the root cause. Do not modify or
delete tests to get a passing result. If a problem resists several
attempts, stop and report to the user.

Then go back to the original requirements. Present each one in text —
a brief description of the requirement followed by what you observed.
For visual changes, include a screenshot or describe the rendered
output. For API or logic changes, show the call result or test output.
Ask the user to confirm each item matches what they expected.

If the change is straightforward and the result is clear, you may
summarize the group rather than walking through each item. Write the
outcome to a verification file.

If the user says a requirement is not met, determine the scope.
A one-line correction can be fixed and re-verified on the spot.
Anything larger — missing behavior, wrong approach, unclear spec —
should not block the current initiative. Record it as a gap or a
new initiative and let the user decide priority.

```
.beyond-code/<slug>/verification.md
---
automated: passed | failed
accepted: confirmed | pending
---
# Automated Checks
# User Acceptance
- [ ] Requirement 1 — user confirmed
- [ ] Requirement 2 — user confirmed
```

Each checklist item should repeat the functional requirement text from
requirements.md so the user can trace what is being verified back to
what was agreed. Automated checks should cover non-functional
requirements where possible.

After the user accepts the verification, move the initiative directory to
`.beyond-code/.archive/<slug>/` to mark it complete.

After archiving, review the accumulated gaps in plan.md. Mention them
to the user — any worth pursuing can become new initiatives.

# Managing multiple pieces of work

Each distinct goal gets its own directory under `.beyond-code/`.

If one depends on another, declare it in `plan.md` frontmatter with
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

If you find one, read its `status.md` to learn which stage it was
in and what was happening. If status.md is missing or unreadable,
reconstruct state from the other files: if plan.md exists but no
tasks are checked off, planning is done and build has not started.
If tasks are checked, resume from the first unchecked one. If you
cannot determine the stage, tell the user what you see and ask.

Then ask the user whether to continue from there or start fresh.
If they want a fresh start, do not delete the old initiative —
move it to `.archive/` or ask the user what to do with it.

# Understanding the project

When the user asks you to study the project, generate documentation
under `.beyond-code/.project/`. Only do this on explicit request —
scanning a project deeply is expensive.

```
.beyond-code/.project/
  index.md            Project overview, core directories, key conventions
  architecture.md     Module responsibilities, dependencies, design decisions
  call-chains.md      Critical code paths and call chains
```

Each file records when it was generated:

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

When reading these files later, compare the recorded commit or date
against the current state. If they differ, warn the user the docs
may be stale.

After completing an initiative, consider whether anything you learned
should be captured here. Propose an update if so.

# Commit preferences

When starting an initiative, check `.beyond-code/config.yaml`. If
absent, ask the user how they want commits handled and write the file.

```
.beyond-code/config.yaml

commit:
  when: per-task | per-plan | manual    # default per-task
  format: project | conventional | user # default project
```

`format: project` means follow the repo's existing commit style.

Read this file at the start of each initiative.
