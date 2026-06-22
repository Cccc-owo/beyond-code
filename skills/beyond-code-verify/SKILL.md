---
name: beyond-code-verify
description: >
  Use when verifying implementation results, running automated
  checks, walking through acceptance criteria with the user, or
  entering the verify phase of beyond-code. Covers automated checks,
  requirements verification, and archiving completed initiatives.
---

# Purpose

Your task is to verify that the implementation matches the agreed
requirements, through automated checks and user acceptance, then
archive the completed initiative.

Run the project's own checks. Detect what tools are available:
a linter (eslint, ruff, biome), a type checker (tsc, mypy),
a build step (npm run build, make), and the test suite (jest,
pytest, go test). Use the exact commands the project already
defines — package.json scripts, Makefile targets, or project
config files. Run them in order: lint first, then typecheck,
then build, then tests.

If no automated tools are found, report this: "No automated checks
detected." Ask the user whether to proceed directly to manual
verification.

When checks fail, find and fix the root cause. If a test fails,
determine why before touching it:
- Real regression? Fix the code.
- Intentional behavior change? Present the old test, the new
  behavior, and ask the user to confirm the test should be updated.
- Test tested implementation detail, not behavior? Note it, don't
  change it — flag as a test quality gap.
Never modify a test to make it pass without understanding which case
applies. If a problem resists several attempts, stop and report to
the user.

Only proceed to requirement verification after automated checks
pass. If checks fail and cannot be fixed, report the failure and
ask the user whether to continue or pause.

Present a compact summary table first: | # | Requirement | Status | Evidence |.
Walk through individual items the user wants to inspect. For each
requirement presented, show what you observed. For visual changes,
include a screenshot or describe the rendered output. For API or
logic changes, show the call result or test output. Ask: "Does this
match expectations, or do you see anything that needs changing?"

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

If the user says a requirement is not met, determine the scope.
A one-line correction can be fixed and re-verified on the spot.
Anything larger — missing behavior, wrong approach, unclear spec —
should not block the current initiative. Record unmet requirements
in verification.md under `## Unmet Requirements`. For each, note:
the requirement, what was observed, and whether it needs a one-line
fix or a new initiative.

## Archive

Create `.beyond-code/.archive/` if it doesn't exist. Move the entire
initiative directory to `.beyond-code/.archive/<slug>/` to mark it
complete. Verify the move by listing `.archive/<slug>/`. If the move
fails, report the error and do not proceed. Double-check: did you
actually move it, not just note it?

After archiving, review the accumulated gaps in `tasks.md`. Mention
them to the user — any worth pursuing can become new initiatives.

If `.beyond-code/.project/` exists, ask the user: "I learned a few
things during this initiative. Should I update any project docs?"
If yes, load the `beyond-code-project-docs` skill to update.

The initiative is now complete. If no other active initiatives exist,
ask the user what to work on next.
