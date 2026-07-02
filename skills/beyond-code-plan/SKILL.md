---
name: beyond-code-plan
description: >
  Use when designing architecture and writing the implementation plan,
  after spec.md is confirmed. Covers Architecture Overview, bite-sized
  task format, Implementation Bounds, Spec Coverage Self-Review, and
  Placeholder Scan.
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

# HARD-GATE: No Code Before Plan Confirmed

You MUST NOT write or modify ANY code. Your ONLY outputs are
plan.md and gate.md updates. Implementation happens in the build
stage AFTER this plan is confirmed.

# Purpose

Create plan.md — an exhaustive implementation plan that leaves the
build agent zero room for interpretation. The build agent MUST be
able to execute the plan without guessing.

# Stage 0: Gate Check

Read `.beyond-code/<slug>/gate.md`. Gate 1 MUST be cleared before
proceeding. If not cleared, STOP and report: "Spec is not confirmed.
Return to think stage."

Read `.beyond-code/<slug>/spec.md`. The plan MUST cover every R.

# Stage 1: Write Architecture Overview

Write 3-5 paragraphs describing:
- Key pieces and their responsibilities
- How they connect
- What existing modules are touched and how
- Order of operations for the primary code path

Write for someone familiar with the codebase.

# Stage 2: Write Global Constraints

List constraints every task inherits. Include exact values:
versions, limits, naming rules, platform requirements. Copy verbatim
from spec.md Constraints section.

```
> **Global Constraints** (every task inherits these):
> - [constraint with exact value]
```

# Stage 3: Write Tasks

Each task MUST follow this format exactly:

```
## Task N: [Action-verb description]

**Covers:** R1, R3
**Files:** Create: `exact/path/a.py`; Modify: `exact/path/b.py:42-60`
**Consumes:** [upstream interface signatures — exact names and types]
**Produces:** [downstream interface signatures — exact names and types]

- [ ] Step 1: [Concrete action with complete code or exact command]
- [ ] Step 2: [Verification step with full command and expected output]
- [ ] Step N: Commit
```

Task rules:
- Each step MUST be completable in 2-5 minutes with one clear result
- Code steps MUST include the complete code, not prose descriptions
- Command steps MUST include the exact command and expected output
- The following are FORBIDDEN as step content: "TBD", "TODO",
  "implement later", "add appropriate error handling", "add validation",
  "handle edge cases", "Similar to Task N", "参考 Task N",
  references to types or functions not defined in any task
  These are plan failures — fix them before presenting.

# Stage 4: Write Implementation Bounds

Write the exhaustive boundary list. The build agent MUST NOT exceed
these bounds:

```
## Implementation Bounds
> BUILD AGENT: You MUST NOT touch any file, create any function,
> add any import, or introduce any dependency NOT listed below.
> If a step requires something not in this list, the plan is
> broken — STOP and report, do not guess.

### File Inventory (exhaustive)
| Path | Action (CREATE/MODIFY) | Purpose |
|------|------------------------|---------|

### API Surface (exhaustive)
| Signature | Location | Visibility |
|-----------|----------|------------|

### Dependencies (exhaustive)
| Package | Version | Purpose |
|---------|---------|---------|

### Prohibited Actions
- [ ] No file creation outside File Inventory
- [ ] No function/class creation outside API Surface
- [ ] No new dependency outside Dependencies list
- [ ] No modification to files NOT in File Inventory
- [ ] No change to existing public interfaces NOT in API Surface
```

# Stage 5: Spec Coverage Self-Review

Before presenting the plan, verify every spec R has ≥1 task:

```
## Spec Coverage Self-Review
| Requirement | Task(s) | Status |
|-------------|---------|--------|
| R1          | T1      | ✅     |
| R2          | —       | ❌     |
```

MUST NOT present the plan with any ❌ rows. Add tasks for missing
requirements or revise the task mapping.

# Stage 6: Placeholder Scan

Search plan.md for forbidden patterns. Report results:

```
## Placeholder Scan
- [ ] "TBD": 0 instances
- [ ] "TODO": 0 instances
- [ ] "implement later": 0 instances
- [ ] "add appropriate error handling": 0 instances
- [ ] "add validation": 0 instances
- [ ] "handle edge cases": 0 instances
- [ ] "Similar to Task": 0 instances
- [ ] All types and functions referenced in tasks are defined in a task
```

# Stage 7: Cross-validate Bounds against Tasks

For each task, verify:
- Every path in the task's Files field appears in File Inventory
- Every import/function/class in the task's code steps appears in
  API Surface or Dependencies
- No task references a file NOT in File Inventory

Fix any mismatch before presenting.

# Stage 8: Present and Update gate.md

Present the user with:
1. The Architecture Overview
2. A summary of tasks (count, order, dependencies)
3. The Spec Coverage table
4. The Implementation Bounds

Wait for explicit confirmation.

After confirmation, update gate.md:

```
## Gate 2: Plan Ready
- [x] plan.md created: `.beyond-code/<slug>/plan.md`
- [x] Spec coverage self-review: N/N covered
- [x] Placeholder scan: 0 found
- [x] User confirmed: <timestamp>
- [x] Gate cleared
```

Return to the beyond-code router.

If "just do it": still write plan.md fully, mark Gate 2 as cleared,
return to router. The plan MUST still pass self-review.
