---
name: beyond-code-build
description: >
  Use when executing a confirmed plan, building features task by
  task, tracking progress through gate.md, or entering the build
  phase of beyond-code. Covers bounds validation, task execution,
  deviation logging, and gap tracking.
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

Execute the task list from plan.md, one task at a time. Stay within
the Implementation Bounds. Log every deviation. Keep gate.md updated
so progress is always visible.

# Step 0: Gate Check and Bounds Validation

Read `.beyond-code/<slug>/gate.md`. Gate 2 MUST be cleared. If not,
STOP and report: "Plan is not confirmed. Return to plan stage."

Read `.beyond-code/<slug>/plan.md`.

Before writing ANY code, validate the Implementation Bounds:
1. For each task's Files field: confirm all paths are in File Inventory
2. For each task's code steps: grep for `import`, `def`, `class` —
   confirm all are in API Surface or Dependencies
3. If ANY mismatch: STOP. Report the mismatch. Do NOT proceed.

This validation MUST complete before Step 1 of Task 1.

# Step N: Execute Each Task

Read `.beyond-code/config.yaml` for commit preferences. Default to
`per-task` if config is absent.

For each task in plan.md, in order:
1. Mark it in-progress in gate.md's Task Execution table
2. Execute each step exactly as written
3. Run verifications as specified — EVIDENCE BEFORE CLAIMS
4. After each action, check: did I touch a file NOT in File Inventory?
   Create a function NOT in API Surface? Add an import NOT in Dependencies?
5. If yes → log as Deviation in gate.md immediately (see below)
6. Handle commit per config:
   - `per-task`: commit now. Record the short commit hash in gate.md.
   - `per-plan`: do NOT commit yet. Record `pending` in gate.md.
   - `manual`: do NOT commit. Record `—` in gate.md.
7. Mark task complete

When a task has `depends_on`: that initiative's gate.md MUST show
Gate 3 (or equivalent) completed before starting.

# Deviation Recording

Write to gate.md under the `## Deviations` section:

```
| Timestamp | Task | Deviation | Rationale |
|-----------|------|-----------|-----------|
| HH:MM     | T3   | Created `cache/__init__.py` (not in File Inventory) | Tests needed package init |
| HH:MM     | T5   | Used `httpx` instead of `requests` (plan said `requests`) | Spec R4 requires async, requests lacks it |
```

Record BEFORE moving to the next step. Do not batch deviations.

# Deviation Threshold

If deviations accumulate to ≥5 total, OR the first deviation has
substantive design impact (new interface, new dependency, changed
data shape, modified a file not in File Inventory for a non-trivial
reason): STOP. Present all accumulated deviations to the user:

```
Deviations from plan.md so far:
| # | Task | Deviation | Rationale |
|---|------|-----------|-----------|
[table]

Options:
1. Accept all — continue
2. Reject specific deviations — I will revert and redo
3. Re-plan — return to plan stage

Which?
```

Do NOT continue until the user responds.

# Gap Recording

If something comes up that the plan did not cover but is NOT a
deviation (discovered issue, future concern), record it:

```
## Gaps
- [ ] <description> — <why out of scope> — <suggested approach>
```

Do NOT expand scope. Gaps become separate initiatives after verify.

# Completed Tasks Validation

Before moving from one task to the next, verify:
- The task's status is recorded in gate.md
- `per-task` mode: git log confirms the recorded commit exists
- `per-plan` / `manual` mode: skip the git-log check — commits are not yet made or not agent-managed
- All verifications in the task's steps passed

# When All Tasks Are Done

Based on config.yaml `commit.when`:

**`per-task`:** All gate.md entries already have individual hashes.
No additional commit needed.

**`per-plan`:** Commit all changes at once:
```bash
git add <all changed files>
git commit -m "feat: <summary of all tasks>"
```
Then update ALL gate.md Task Execution entries with the single commit hash:
```
| Task | Commit | Status |
|------|--------|--------|
| T1   | abc1234 | ✅     |
| T2   | abc1234 | ✅     |
| T3   | abc1234 | ✅     |
```

**`manual`:** Leave all entries as `—`. User commits separately.

Update gate.md with the final table state:
```
## Gate 3: Task Execution
| Task | Commit | Status |
|------|--------|--------|
| T1   | abc1234 | ✅     |  ← per-task: unique hash
| T2   | —       | ✅     |  ← manual: agent does not commit
```

Present a completion summary:
- Files changed, scope of each change
- Deviations logged (if any)
- Gaps recorded (if any)
- If per-plan: the single commit hash covering all tasks

Return to the beyond-code router to enter verify stage.
