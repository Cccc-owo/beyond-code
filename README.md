# Beyond Code

A lightweight, natural-language-driven interaction skill for coding agents.

Make the agent understand your intent. Make the plan pass your review. Make every action start with your consent. Make the result earn your acceptance.

[English](.) | [中文](README_zh-CN.md)

## Why This Exists

In my vibe coding practice, I tried several AI-process-driven skills: [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec), [obra/superpowers](https://github.com/obra/superpowers), [open-gsd/gsd-core](https://github.com/open-gsd/gsd-core). They are powerful, but I ran into problems.

Some are clean and lightweight, yet after generating a plan they jump straight to implementation without waiting for my confirmation. I often have to ask the agent to walk me through the approach and revise the plan myself. The final verification is also shallow — I still find quality issues when I test things by hand.

Some are thorough and disciplined, but every step requires CLI interactions or bash commands to gather state, burning context on infrastructure instead of thinking.

These skills share a deeper issue: they trust the agent too much and rely on their own internal terminology to constrain it. So I stopped using them and found that with the same token budget, a flexible process of my own could work better. I designed this skill to help developers and agents collaborate more effectively and produce code that actually matches expectations.

## Philosophy

User intent comes first. Code serves the user, so the agent must clarify what the user actually wants. The plan must be reviewed and approved before execution starts. The outcome must satisfy the user before the work is declared done.

The process is a means, not an end. Stay light. Let user feedback determine what happens next. If a task is simple enough, skip the ceremony.

Invisible is best. The process should blend naturally into conversation and development. No jargon to confuse, no heavy workflow to frustrate. This skill is simply a distillation of how I already work.

## Install

```bash
npx skills add Cccc-owo/beyond-code
```

Triggered by natural language or manual invocation. Say "let's plan first", or the agent should activate automatically when the task involves architectural decisions or spans multiple files.

## Flow

```txt
triggered /beyond-code

          |
          v
     [check scope]
          |
    +-----+-----+
    |           |
  trivial    needs design
    |           |
    v           v
  just do    +-- THINK ---------+
  (no skill) |  one question    |
             |  at a time       |
             |                  |
             |  skip:           |
             |  "just plan it"  |
             +--------+---------+
                      | requirements confirmed
                      v
             +-- PLAN ----------+
             |  architecture    |
             |  + task list     |
             |                  |
             |  !! gate:        |
             |  must confirm    |
             |  shortcut:       |
             |  "just do it"   |
             +--------+---------+
                      | user says OK
                      v
             +-- BUILD ---------+
             |  task by task     |
             |  stay in scope    |
             |  discoveries ->   |
             |  Gaps             |
             |  stuck? -> stop   |
             |  + report         |
             +--------+---------+
                      | all tasks done
                      v
             +-- VERIFY --------+
             |  auto checks     |
             |  + user accept   |
             |                  |
             |  !! gate:        |
             |  must accept     |
             +--------+---------+
                      | user confirms
                      v
             +-- ARCHIVE -------+
             |  move to         |
             |  .archive/       |
             +------------------+
```

Saying something like "study this project" triggers a deep scan that generates project documentation for the agent.

```txt
beyond-code/.project/
  ├── index.md          project overview, core directories, key conventions
  ├── architecture.md   module responsibilities, dependencies, design decisions
  └── call-chains.md    critical code paths and call chains
```

## Directory Structure

```txt
.beyond-code/
├── <slug>/
│   ├── requirements.md      # confirmed requirements
│   ├── plan.md              # plan + tasks + discoveries left undone
│   ├── status.md            # current stage
│   └── verification.md      # auto checks + user acceptance
├── .archive/                # completed initiatives
└── .project/                # project context docs (manual trigger)
    ├── index.md             #   overview, core dirs, conventions
    ├── architecture.md      #   module responsibilities, deps, design decisions
    └── call-chains.md       #   critical code paths
```

## License

[MIT](LICENSE)
