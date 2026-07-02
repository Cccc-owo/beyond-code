# Beyond Code

一个轻量、自然语言驱动的 Coding Agent 交互 Skill

让 Agent 明确你的需求。强制方案经过你的审核。约束 Agent 的每一步行动。要求 Agent 拿出证据而非口头声称。

[English](README.md) | 中文

## 为什么会有这个 Skill？

在 Vibe Coding 实践中，我曾试过几个 AI 流程主导的 Skills，比如 [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec), [obra/superpowers](https://github.com/obra/superpowers), [open-gsd/gsd-core](https://github.com/open-gsd/gsd-core)。

在感受它们的强大的同时，我也遇到了一些问题。有些 Skills 轻量但 plan 不细就催你确认，验收浮于表面。有些认真但每一步都需要 CLI 交互，上下文成本高。

更重要的是，它们都过于信任 Agent，且偏好用自己定义的术语约束 Agent。于是我决定抛开这些 Skills，用自己的流程设计了这个 Skill，来让人类更好地与 Agent 交互，得到更符合预期的代码。

## 设计理念

**需求先于代码。** Agent 在 spec 未确认前 MUST NOT 写任何代码。每个需求必须有可追溯的验收标准。

**穷举边界，禁止越界。** plan 必须列出 build agent 可以触碰的所有文件、函数、依赖。超出清单的一律是 deviation，累计触发用户审查。

**证据，而非声称。** EVIDENCE BEFORE CLAIMS。禁止 "应该没问题"、"看起来对"，必须跑命令、看输出、再下结论。

**用硬语言，不用软建议。** 全 skill 使用 RFC 2119 关键词（MUST, MUST NOT, NEVER, HARD-GATE, STOP）。跳过门禁 = 违反 skill。

## 使用

```bash
npx skills add Cccc-owo/beyond-code
```

通过自然语言触发或手动调用，如"先计划一下"。仅在极小规模项目（如 ~100 行的脚本）的 trivial bug fix + 用户明确说"skip beyond-code"时才可以跳过。

## 流程

```
Think ──[HARD-GATE]──→ Plan ──[HARD-GATE]──→ Build ──[HARD-GATE]──→ Verify

  │                      │                       │                        │
  └── spec.md            └── plan.md             └── commits +           └── 检查 +
       (做什么 +              (怎么做 +                  deviations             验收 +
        验收标准)             穷举边界)                 记录于 gate.md          归档)
```

**Think** — 一次一问。Scope Check 检测多子系统 feature，自动拆分为多个 initiative。产出 `spec.md`，Given/When/Then 格式，禁止模糊动词（"支持"、"增强"、"整合"）。

**Plan** — 架构概览 + bite-sized tasks（含精确文件路径、完整代码、预期命令输出）。穷举 Implementation Bounds（文件清单、API 清单、依赖清单、禁止行为）。自检 spec 覆盖 + 占位符扫描后呈现。

**Build** — Step 0 先验证 Bounds 无冲突。task 在边界内执行。Deviation 实时记录；≥5 条或首个实质影响 deviation 触发 STOP 并展示给用户。commit 行为尊重 config.yaml。

**Verify** — 自动化检查 + EVIDENCE BEFORE CLAIMS。逐需求用户验收。完成归档至 `.archive/`。

## 工作目录结构

```
.beyond-code/
├── config.yaml               # commit 偏好设置
├── <slug>/
│   ├── spec.md               # 做什么 + 验收标准
│   ├── plan.md               # 怎么做 + 穷举边界 + task 列表
│   └── gate.md               # 进度账本 — 唯一状态源
├── .archive/                 # 已完成的 initiative
└── .project/                 # 项目上下文文档（手动触发）
```

## Skill 架构

```
beyond-code (路由)
  ├── think      → 产出 spec.md
  ├── plan       → 产出 plan.md
  ├── build      → 执行 task，记录 deviation
  ├── verify     → 检查 + 验收 + 归档
  └── project-docs → deep scan（显式触发）
```

每个子 skill 内嵌 Terminology Reference 表格，并重申禁止代码前的禁令。每个子 skill 加载时检查 gate.md 确认上一关已通过。

## LICENSE

[MIT](LICENSE)
