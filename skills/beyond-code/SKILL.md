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

**STAGE TRANSITION** — Update `.beyond-code/<slug>/status.md`:
set `stage: think`, `gate: none`, `next: clarify requirements with user`.

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
gate: pending_confirmation | none
updated: YYYY-MM-DD
---
next: <immediate next action>
last_completed: <what was just finished>
```

`gate: pending_confirmation` means the agent is waiting for the user
to confirm before moving to the next stage. `gate: none` means the
agent may proceed. After each stage transition and after each
completed task, update `next` and `last_completed` so a fresh session
knows exactly what to do without reading any other file.

After writing the files, ask the user to confirm the requirements,
then move to planning. Write requirements with status: draft. Change
to confirmed only after the user agrees the requirements are correct.

After writing requirements.md, update status.md:
set `gate: pending_confirmation`, `next: await user confirmation`.
After the user confirms, update status.md:
set `gate: none`, `next: proceed to plan stage`.

The slug is lowercase letters and hyphens, derived from the request.

Before wrapping up, ask the user if they have any remaining questions.
Mention what was not yet discussed — any directions or tradeoffs left
unexplored — so the user can decide whether to continue or move on.

When the user indicates the requirements are right, or says
"let's plan", move on. The user can also say "just do it" to skip
confirmation gates entirely — still write requirements, design, and
task files, but proceed directly to build.

When moving on, tell the user what you understood and what comes next.
For example: "Here's what I heard... Ready to plan?"

# Before implementing

**STAGE TRANSITION** — Update `.beyond-code/<slug>/status.md`:
set `stage: plan`, `gate: none`, `next: create design.md and tasks.md`.

## Write the design

Start with `design.md` — a document that captures the design thinking
separate from the execution checklist. The design must be clear enough
that someone familiar with the codebase can understand what will
change and why, without reading the task list.

Create each section below if it applies. If a section is not needed,
skip it with a one-line note explaining why.

The complete file skeleton:

```
.beyond-code/<slug>/design.md

# Data Model
<!-- omit if no new or changed data structures -->

# Data Flow
<!-- omit if data stays within a single component -->

# Components & Contracts
<!-- omit if no new or changed component interfaces -->

# Impact on Existing Modules
<!-- always write -->

# Architecture Overview
<!-- always write -->
```

**Data Model** — write when introducing new data structures or
modifying existing data shapes. Define the key data structures
involved. For each one, describe its shape, fields, types, and
invariants. Use pseudo-types or tables — whatever makes the
structure unambiguous.

**Data Flow** — write when data passes between 2+ components or
modules. Describe the path data takes through the system for the
primary use case. Which component produces it, which one transforms
it, which one consumes it. A text diagram (ASCII or Mermaid) or
numbered sequence is fine.

**Components & Contracts** — write when adding new components or
changing existing component interfaces. For each component, describe:
- Responsibility: what it does in one sentence.
- Interface: what callers must provide (inputs, preconditions) and
  what they can expect back (outputs, postconditions, error modes).
- Dependencies: what other components it calls.
Do not describe implementation internals — that belongs to the build
phase.

**Impact on Existing Modules** — always write. List each existing
module, file, or directory that this change touches. For each one,
describe how it is affected — new import, changed interface,
modified logic, removed code. Be specific enough that someone can
judge the blast radius without reading the task list.

**Architecture Overview** — always write. Synthesise the sections
above into a prose overview, three to five paragraphs. Describe the
key pieces and their responsibilities, how they connect, what
existing modules are touched and how, and the order of operations
for the primary code path. Write for someone familiar with the
codebase — no need to explain what the project does, only how this
change fits into it.

After writing `design.md`, review it at least once. Verify: do the
data structures match the data flow? Do the component interfaces
satisfy the described contracts? Are all affected modules listed in
Impact?

## Write the task list

Create `tasks.md` — the execution checklist. Build-phase agents work
from this file alone; they should not need to re-read `design.md`.

```
.beyond-code/<slug>/tasks.md
---
depends_on: <another-slug> | none
---

# Files in Scope

# Tasks
- [ ] Step 1 — concrete, verifiable action
- [ ] Step 2 — concrete, verifiable action (depends on: Step 1)

## Gaps
<!-- Issues discovered during implementation that are out of
     scope for this initiative. Address as a separate initiative. -->
```

**Files in Scope**: list exact file paths where the change is known
in advance. Where a change touches a directory broadly, list the
directory and the kind of files affected. Be specific enough that the
build phase knows what it may and may not touch.

**Tasks**: a checklist of concrete, verifiable actions. Each task must
be completable in one sitting with a clear result. Order by natural
build sequence — foundation before upper layers. Name each task with
an action verb: "Add the theme context provider" not "Theme context".
If a task is not self-explanatory, expand it with enough detail that
someone else can pick it up and know what to do. If a task depends on
another, note it.

Before finalising, review the task list against the design: does
every design component have corresponding tasks? Are there tasks for
integrating with the modules listed in Impact?

If the task list grows beyond what can be done in a single session
or spans two clearly separate concerns, split it into multiple
initiatives. Declare the dependency with `depends_on` so order is
preserved.

## Present the plan

Present the design and task list to the user. Give the user a choice:
- Walk through each section of `design.md` in detail, then the task
  summary
- Review the architecture overview and a summary of each section

Then stop. Summarise the key points — what will change, in what
order — so the user knows what they are confirming.

Confirmation means: "OK", "go ahead", "confirmed", "start",
"just do it".

Not confirmation: "looks good", "makes sense", "seems fine",
"alright", "sure", "ok", any reply that sounds like discussion or
tentative agreement. When you see these, do not proceed. Ask again
explicitly: "Shall I start implementing?"

After writing the design and task list, update status.md:
set `gate: pending_confirmation`, `next: await user plan approval`.

If they say "just do it", proceed but still write the design and
task files first.

If the user requests changes, revise the design and task list and
present again. If this happens repeatedly without convergence, offer
to capture the open questions and proceed with what has been agreed
so far.

# While implementing

**STAGE TRANSITION** — Update `.beyond-code/<slug>/status.md`:
set `stage: build`, `gate: none`, `next: <first task description>`.

Check `.beyond-code/config.yaml` for commit preferences.
If no config exists, ask the user and write one.

Commits: if set to per-task, commit after each completed task. If set
to per-plan, commit once after all tasks. If set to manual, do not
commit on your own. Match the format from config (project convention,
conventional style, or user-written).

Follow the task order. Mark each one `- [x]` in `tasks.md` when done.

If tasks declare a dependency, wait for the prerequisite to complete.
If two tasks are independent, you may run them in parallel.

After each task, update three things so a fresh session knows exactly
what was happening:
- The checkbox in `tasks.md`
- `status.md` field `next` to the next task description
- `status.md` field `last_completed` to the task just finished

When committing (per-task or per-plan), include the task description
in the commit message.

Follow the existing patterns in each file. When you encounter a file
not in the task list:
- If the tasks cannot be completed without changing it, update
  `tasks.md` first, then proceed.
- If it is an improvement that can wait, note it under `## Gaps`.

If something comes up that the task list did not cover, add it to the
`## Gaps` section of `tasks.md`. Do not expand scope within the
current initiative. Address gaps as a separate initiative once the
current one is complete.

A task is a candidate for sub-agent delegation when it is
self-contained — someone can complete it by reading only the task
description, the relevant files, and this skill. If a task is
tightly coupled to decisions being made in other tasks, keep it in
the main session. When delegating, include exactly what the
sub-agent needs: the task, the file paths, the relevant design
context, and the path to the initiative directory so it can read
and update the tasks and status files.

If a task fails, try the obvious fixes first: correct a typo, fix a
wrong path or import, adjust an assumption that proved incorrect,
rerun after a transient failure. If a fix requires changing the
approach or touching code outside the task list, it is not obvious —
stop and report what you tried. Do not loop silently.

If a failing task blocks tasks that depend on it, report which
subsequent tasks are now blocked and ask the user whether to pause
the initiative, re-plan, or skip the blocking task and proceed
with independent work.

When all tasks are done, update status.md:
set `next: run automated checks`, `last_completed: all tasks`.
Tell the user what was built and ask if they are ready to verify.

# After implementing

**STAGE TRANSITION** — Update `.beyond-code/<slug>/status.md`:
set `stage: verify`, `gate: none`, `next: run automated checks`.

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

## Archive

After the user accepts the verification, move the entire initiative
directory to `.beyond-code/.archive/<slug>/` to mark it complete.
Double-check: did you actually move it, not just note it?

After archiving, review the accumulated gaps in `tasks.md`. Mention
them to the user — any worth pursuing can become new initiatives.

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
