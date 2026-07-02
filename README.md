# Beyond Code

A lightweight, natural-language-driven interaction skill for coding agents.

Make the agent understand your intent. Force the plan through your review. Constrain every action to your consent. Demand evidence before acceptance.

English | [中文](README_zh-CN.md)

## Why This Exists

In my vibe coding practice, I tried several AI-process-driven skills: [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec), [obra/superpowers](https://github.com/obra/superpowers), [open-gsd/gsd-core](https://github.com/open-gsd/gsd-core). They are powerful, but I ran into problems.

Some are clean and lightweight, yet after generating a plan they jump straight to ask for my permission without explaining the plan. Some are thorough and disciplined, but every step requires CLI interactions or bash commands to gather state, burning context on infrastructure instead of thinking.

These skills share a deeper issue: they trust the agent too much and rely on their own internal terminology to constrain it. So I stopped using them and found that with the same token budget, a flexible process of my own could work better. I designed this skill to help developers and agents collaborate more effectively and produce code that actually matches expectations.

## Philosophy

**User intent before code.** The agent MUST NOT write code until the spec is confirmed. Every requirement must be traceable to a concrete acceptance criterion.

**Plan exhaustively, execute within bounds.** The plan must list every file, function, and dependency the build agent may touch. Anything not in that list is a deviation — and deviations trigger review.

**Evidence, not claims.** EVIDENCE BEFORE CLAIMS. No "should be fine." No extrapolation from old output. Run the command fresh, show the output, then make your claim.

**The process uses hard language, not soft suggestions.** The skill suite uses RFC 2119 keywords (MUST, MUST NOT, NEVER, HARD-GATE, STOP) throughout. An agent that skips a gate violates the skill.

## Install

```bash
npx skills add Cccc-owo/beyond-code
```

Triggered by natural language or manual invocation. Say "let's plan first", or the agent should activate automatically when the task involves architectural decisions or spans multiple files. The ONLY skip condition: a trivial bug fix in a small-scale project (e.g. a script under ~100 lines) where the user explicitly says "skip beyond-code".

## Flow

```
Think ──[HARD-GATE]──→ Plan ──[HARD-GATE]──→ Build ──[HARD-GATE]──→ Verify

  │                      │                       │                        │
  └── spec.md            └── plan.md             └── commits +           └── checks +
       (what +                 (how +                  deviations              acceptance +
        acceptance)            exhaustive              logged in               archive
                               bounds)                 gate.md)
```

**Think** — One question at a time. Scope Check detects multi-subsystem features and splits them into separate initiatives. Produces `spec.md` with Given/When/Then requirements. Vague verbs ("support", "integrate", "enhance") are forbidden.

**Plan** — Architecture overview + bite-sized tasks with exact file paths, complete code, and expected command output. Exhaustive Implementation Bounds (File Inventory, API Surface, Dependencies, Prohibited Actions). Spec Coverage self-review + Placeholder Scan before presentation.

**Build** — Step 0 validates Implementation Bounds. Tasks execute within bounds. Deviations logged in real-time; ≥5 or first substantive deviation triggers STOP and user review. Commit behavior respects config.yaml (per-task / per-plan / manual).

**Verify** — Automated checks with EVIDENCE BEFORE CLAIMS. Per-requirement user acceptance. Archive completed initiatives to `.archive/`.

## Directory Structure

```
.beyond-code/
├── config.yaml               # commit preferences
├── <slug>/
│   ├── spec.md               # what to build + acceptance criteria
│   ├── plan.md               # how to build + exhaustive bounds + tasks
│   └── gate.md               # progress ledger — single source of truth
├── .archive/                 # completed initiatives
└── .project/                 # project context docs (manual trigger)
```

## Skill Architecture

```
beyond-code (Router)
  ├── think      → produce spec.md
  ├── plan       → produce plan.md
  ├── build      → execute tasks, log deviations
  ├── verify     → checks + acceptance + archive
  └── project-docs → deep scan (explicit trigger only)
```

Every sub-skill embeds a Terminology Reference block and re-declares the code-before-spec prohibition. Each checks gate.md before proceeding.

## License

[MIT](LICENSE)
