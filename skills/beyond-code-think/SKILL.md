---
name: beyond-code-think
description: >
  Use when clarifying requirements before coding, the user's intent
  is unclear, needs to narrow down what to build through questions,
  or entering the think phase of beyond-code. Covers one-question-
  at-a-time interviewing, requirements file format, and status
  tracking setup.
---

# Purpose

Your task is to clarify the user's intent through focused questioning
and capture the result in concrete, reviewable documents. Do not plan
architecture or implementation here — that comes after the user
confirms these requirements.

Ask one question at a time. Wait for the user's answer before asking
the next. Each question goes deeper based on the previous answer.
If 3+ independent questions remain and answering one won't change the
next, present them as a numbered list — but let the user answer each.

Ask questions that narrow down what is unclear. Each question should
build on the last answer, not follow a predefined list. If the user
describes a UI, ask about interactions and edge states. If they
describe a backend, ask about data shape and failure modes. Let the
substance of the request guide what you ask next.

Do not invent scenarios. If an answer is vague, do not fill in the
gaps yourself — ask a follow-up or offer your best interpretation
and ask if it is correct. Encourage the user to ask you questions back.

Shape every question to expose a design choice, not fill a blank.
Include your understanding, present concrete options appropriate to
the decision, and state your recommendation with reasoning. For
simple binary choices, a brief tradeoff note is enough. The user
can pick an option, reject all, or build on one — the point is to
give them something to react to.

Wrong:
"Which database should we use?"

Right:
"Persistence — I see two paths. SQLite for zero-setup single-file
simplicity, good if this is a local tool. Postgres if you expect
concurrent writes or need JSONB queries. I'd start with SQLite and
swap later if needed. Which direction fits?"

If the user asks for something technically impossible or self-
contradictory, say so directly. Explain the constraint and offer
the closest feasible alternative.

When the picture is clear, write the summary to a requirements file:

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

Create a status file (schema defined in beyond-code, see Initiative
Directory Layout):

```
.beyond-code/<slug>/status.md
---
stage: think
gate: pending_confirmation | none
updated: <today's date>
---
next: <immediate next action>
last_completed: <what was just finished>
```

`gate: pending_confirmation` means the agent is waiting for the user
to confirm before moving to the next stage. `gate: none` means the
agent may proceed. After each stage transition and after each
completed task, update `next` and `last_completed` so a fresh session
knows exactly what to do without reading any other file.

The slug is lowercase letters and hyphens, derived from the request.

After writing the files, ask the user to confirm the requirements.
Write requirements with status: draft. Change to confirmed only after
the user agrees the requirements are correct.

After writing requirements.md, update status.md:
set `gate: pending_confirmation`, `next: await user confirmation`.
After the user confirms, update status.md:
set `gate: none`, `next: proceed to plan stage`.

Before wrapping up, present a mapping of what you heard to what you
wrote. List each key point the user made and which requirement it
maps to in requirements.md. Example:

> - You mentioned offline support → captured as Requirement 2
> - You want email-only auth, no social login → captured as
>   Requirement 3, constraint added
>
> Does this mapping look accurate? Anything I miscategorised?

This exposes your reasoning — if a mapping is wrong, the user can
correct it before you proceed.

After confirming, ask if they have any remaining questions. Mention
what was not yet discussed so they can decide whether to continue
or move on.

When the user indicates the requirements are right, or says
"let's plan", move on. The user can also say "just do it" to skip
confirmation gates entirely — write requirements as draft, update
status.md to stage: plan, then load `beyond-code-plan` to create
design and tasks. Plan will hand off to build without confirmation.

When moving on, update status.md:
set `stage: plan`, `gate: none`, `next: create design.md and tasks.md`.
Then load `beyond-code-plan` to proceed.
