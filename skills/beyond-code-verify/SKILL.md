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
outcome to a verification file:

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
should not block the current initiative. Record it as a gap or a
new initiative and let the user decide priority.

## Archive

After the user accepts the verification, move the entire initiative
directory to `.beyond-code/.archive/<slug>/` to mark it complete.
Double-check: did you actually move it, not just note it?

After archiving, review the accumulated gaps in `tasks.md`. Mention
them to the user — any worth pursuing can become new initiatives.

If `.beyond-code/.project/` exists, ask the user: "I learned a few
things during this initiative. Should I update any project docs?"
If yes, load the `beyond-code-project-docs` skill to update.
