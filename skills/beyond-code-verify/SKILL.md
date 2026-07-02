---
name: beyond-code-verify
description: >
  Use when verifying implementation results, running automated checks,
  walking through acceptance criteria with the user, or entering the
  verify phase of beyond-code. Covers automated checks with evidence,
  per-requirement user acceptance, and archiving completed initiatives.
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

# Purpose

Verify that the implementation matches the agreed spec, through
automated checks and user acceptance, then archive the completed
initiative.

# Stage 0: Gate Check

Read `.beyond-code/<slug>/gate.md`. Gate 3 MUST show all tasks
complete with commit hashes. If not, STOP and report: "Not all
tasks are complete. Return to build stage."

# Stage 1: Automated Checks

Detect the project's tooling: linter, type-checker, build step,
test suite. Use the exact commands defined in the project (scripts,
Makefile, config). MUST run in order: lint → typecheck → build → tests.

**EVIDENCE BEFORE CLAIMS**: For each check, you MUST run the full
command fresh, capture the output, and present the evidence:

```
Lint: `npm run lint`
→ 0 errors, 0 warnings

Typecheck: `npx tsc --noEmit`
→ exit 0

Tests: `npm test`
→ 42/42 passing
```

NEVER claim a check passed without running it. NEVER use output
from a previous run. NEVER extrapolate from partial results.

If no automated tools are detected, MUST report: "No automated checks
detected." Ask whether to proceed to manual verification.

When checks fail, MUST fix the root cause. If a fix resists 3 attempts,
STOP and report to the user.

# Stage 2: Requirement Verification

MUST present a summary table:

```
| # | Requirement | Status | Evidence |
|---|-------------|--------|----------|
| 1 | R1: [text from spec.md] | ✅ | <what you observed> |
| 2 | R2: [text from spec.md] | ⬜ | pending user review |
```

For each R, MUST present what you observed and ask: "Does this match
expectations?"

MUST update gate.md:

```
## Gate 4: Verification
- [ ] Automated checks passed
- [ ] R1: user confirmed
- [ ] R2: user confirmed
```

If a requirement is not met:
- One-line fix → fix and re-verify on the spot
- Larger gap → do not block verification. Record as unmet in
  gate.md, note whether it needs a one-line fix or a new initiative.

# Stage 3: Archive

When all confirmed R's pass and automated checks pass:

1. Create `.beyond-code/.archive/` if needed
2. MOVE the entire initiative directory to `.beyond-code/.archive/<slug>/`
3. Verify the move:
```bash
ls .beyond-code/.archive/<slug>/
```
4. Update gate.md:
```
## Archive
- [x] Moved to .beyond-code/.archive/<slug>/
```

If the move fails, MUST report the error. MUST NOT proceed.

# Stage 4: Post-Archive

MUST review accumulated Gaps in gate.md and present them to the user:
"These gaps were recorded during implementation. Any worth pursuing
as new initiatives?"

If `.beyond-code/.project/` exists, ask: "Should I update any
project docs?" If yes, load `beyond-code-project-docs`.

The initiative is complete. If no other active initiatives exist,
ask what to work on next.
