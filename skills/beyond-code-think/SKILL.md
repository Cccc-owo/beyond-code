---
name: beyond-code-think
description: >
  Use when clarifying requirements before coding, the user's intent
  is unclear, needs to narrow down what to build through questions,
  or entering the think phase of beyond-code. Covers Scope Check,
  Given/When/Then spec format, and gate.md management.
---

# Terminology Reference

This skill uses RFC 2119 keywords:

| Keyword | Meaning |
|---------|---------|
| MUST / REQUIRED | Absolute obligation. |
| MUST NOT | Absolute prohibition. |
| NEVER | Zero-exception prohibition. |
| HARD-GATE | Must pass before next stage. |
| STOP | Cease current action. |
| ONLY | Exclusive action — no other path permitted. |
| MAY | Agent discretion. |
| EVIDENCE BEFORE CLAIMS | No success claim without fresh command output. |

# HARD-GATE: No Code Before Spec

You MUST NOT write or modify ANY code. Your ONLY outputs are
spec.md and gate.md. Implementation happens in the build stage.

# Purpose

Clarify user intent through focused questioning. Capture the result
in spec.md with verifiable acceptance criteria. Do NOT plan
architecture or implementation — that comes after the user confirms
this spec.

# Stage 0: Gate Check

Read `.beyond-code/<slug>/gate.md`.

If Gate 1 is already cleared, report this and STOP — the initiative
should proceed to plan, not re-think. If gate.md is missing, create
it with an empty Gate 1 checklist.

# Stage 1: Scope Check

Before asking clarifying questions, assess: does this feature contain
≥2 independently deliverable subsystems?

If YES — ask the user to confirm the subsystem split before writing
any spec. Present a Module Breakdown:

```
| Module | Description | Initiative Slug | depends_on |
|--------|-------------|-----------------|------------|
| ...    | ...         | ...             | none       |
```

Each module becomes a separate initiative. Start with the first one;
the rest get summarized in the first spec's Module Breakdown table
for later.

If NO — proceed to clarifying questions.

# Stage 2: Clarifying Questions

Ask one question at a time. Wait for the answer.
Each question builds on the last answer, not a predefined list.

Shape every question to expose a design choice, not fill a blank.
Include your understanding, present concrete options, state your
recommendation with reasoning.

Wrong:
"Which database should we use?"

Right:
"Persistence — I see two paths. SQLite for zero-setup single-file
simplicity, good if this is a local tool. Postgres if you expect
concurrent writes or need JSONB queries. I'd start with SQLite and
swap later if needed. Which direction fits?"

Do NOT invent scenarios. If an answer is vague, do NOT fill gaps
yourself — ask a follow-up or offer your best interpretation and
ask if it is correct.

If something is technically impossible, say so directly. Explain
the constraint and offer the closest feasible alternative.

# Stage 3: Write spec.md

When the picture is clear, write spec.md:

```
---
slug: <initiative-slug>
status: draft | confirmed
---

# Feature: [one-line description]

## R1: [description — MUST use action verbs, MUST NOT use "support", "integrate", "enhance", "optimize"]

**Given** [precondition]
**When** [trigger]
**Then** [observable result — MUST be manually verifiable]

## Constraints

- [hard boundary]
```

Every R MUST:
- Use concrete action verbs (e.g. "display", "call", "respond", "log", "block")
- Have a manually verifiable Then clause
- NOT use vague verbs ("support", "integrate", "enhance", "optimize")

If the Scope Check identified sub-systems, append:

```
## Module Breakdown

| Module | Initiative Slug | depends_on |
|--------|-----------------|------------|
```

# Stage 4: Update gate.md

Append to Gate 1:

```
## Gate 1: Spec Confirmed
- [x] spec.md created: `.beyond-code/<slug>/spec.md`
- [ ] User confirmed
```

# Stage 5: Present and get confirmation

Present a mapping of what you heard to what you wrote. List each key
point the user made and which R it maps to in spec.md:

> - You mentioned X → captured as R1
> - You want Y → captured as R2, constraint added
>
> Does this mapping look accurate?

Set spec.md status to `draft`. Change to `confirmed` ONLY after the
user agrees the spec is correct.

When confirmed, update gate.md Gate 1:
```
- [x] User confirmed: <timestamp>
- [x] Gate cleared
```

Then return to the beyond-code router.

If the user says "just do it": write spec.md as draft, update gate.md
Gate 1 as cleared, and return to the router directly.
