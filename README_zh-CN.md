# Beyond Code

一个轻量、自然语言驱动的 Coding Agent 交互 Skill

让 Agent 明确你的需求，让做出的计划经过你的审核，让 Agent 的行动始于你的同意，让最终成果经过你的检验。

中文 | [English](.)

## 为什么会有这个 Skill？

在 Vibe Coding 实践中，我曾试过几个 AI 流程主导的 Skills，比如 [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec), [obra/superpowers](https://github.com/obra/superpowers), [open-gsd/gsd-core](https://github.com/open-gsd/gsd-core)。

在感受它们的强大的同时，我也遇到了一些问题。

有些 Skills 的流程相对轻量干净，但生成计划方案后不等我确认细节就直接问我开始写代码了，我多数时候不放心，故还需要再让 Agent 讲讲方案思路和修改计划；此外最后计划的验证也相对敷衍，自己实际测试的时候还是会发现有一点质量问题。

有些 Skills 的流程比较重，也执行地比较认真，但它每一步都需要通过 cli 交互或 bash 命令来获取信息，这无疑增加了一定的上下文成本。

这些 Skills 都有潜在的一个问题，即过于信任 Agent，且偏好用自己定义的术语来约束 Agent。于是我决定抛开这些 Skills，发现在同样 tokens 用量的情况下，可能用自己的一套灵活的流程会更为高效。于是我设计了这个 Skill，来让人类更好地与 Agent 交互，得到更符合预期的代码。

## 设计理念

以用户需求为第一优先——代码是服务于用户的，Agent 自然需要明确用户的具体需求，实现方案也需要用户进行严格审核并同意才开始执行，最终成果还是得让用户满意才算真正达成任务。

流程是手段而不是目的——轻流程，根据用户的反馈来判断目前、下一步该怎么做。如果是并不需要太复杂的思考和讨论的轻量需求，就不需要经过复杂的流程。

不存在就是最好的存在——让流程自然融入对话和开发，不必用术语让人迷惑，不必用复杂的流程让人烦躁。这个 Skill 的流程本身就是对我自身开发过程的总结。

## 使用

```bash
npx skills add Cccc-owo/beyond-code
```

通过自然语言触发或手动调用，如「先计划一下」，或者任务较为复杂，需要架构决策时 Agent 应该自行调用。

## 流程

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

通过类似「研究一下当前项目」的表述，可以触发deep scan，这会生成项目文档供 Agent 参考。

```txt
beyond-code/.project/
  ├── index.md          项目入口、核心目录、关键约定
  ├── architecture.md   模块职责、依赖关系、设计决策
  └── call-chains.md    关键调用链/代码路径
```

## 工作目录结构

```txt
.beyond-code/
├── <slug>/
│   ├── requirements.md      # 确认后的需求
│   ├── plan.md              # 方案 + 任务 + 发现但没做的事
│   ├── status.md            # 当前阶段
│   └── verification.md      # 自动检查 + 用户验收
├── .archive/                # 已完成的 initiative
└── .project/                # 项目上下文文档（手动触发
    ├── index.md             #   概览、核心目录、约定
    ├── architecture.md      #   模块职责、依赖、设计决策
    └── call-chains.md       #   关键调用链
```

## LICENSE

[MIT](LICENSE)
