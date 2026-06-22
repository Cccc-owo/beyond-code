---
name: beyond-code-plan
description: >
  Use when designing architecture before implementing, creating a
  plan with data models and component contracts, writing design and
  task documents, or entering the plan phase of beyond-code. Covers
  design.md five-section format, tasks.md checklist, and confirmation
  gates.
---

# Purpose

Your task is to create a design document and a task list that are
detailed enough for someone familiar with the codebase to understand
what will change and why, then get the user's explicit confirmation
before any code is written.

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

# Tradeoffs & Risks
<!-- always write, even if only to state why none exist -->

# Impact on Existing Modules
<!-- always write, even if only to state why none exist -->

# Architecture Overview
<!-- always write — write this last, after all other sections -->
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

**Tradeoffs & Risks** — always write. Name at least one design
tradeoff (why you chose approach A over B) and at least one risk
(what could go wrong, what assumption could prove false). Write
for a reviewer who needs to judge whether the plan is sound before
giving the go-ahead.

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
Impact? If any answer is no, revise the affected sections before
presenting to the user.

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

Wait for an unambiguous confirmation: the user must say "go ahead",
"start", "build it", "just do it", or any phrase that clearly means
proceed. If the response is ambiguous ("looks good", "sure", "ok"),
ask explicitly: "Shall I start implementing?"

After the user confirms, update status.md:
set `stage: build`, `gate: none`, `next: <first task description>`.
Then load `beyond-code-build` to proceed.

If they say "just do it", proceed but still write the design and
task files first.

If the user requests changes, revise the design and task list and
present again. After 3 revisions without convergence, stop. Capture
the open questions in a `## Unresolved` section in design.md,
present what has been agreed, and ask the user how to proceed.
