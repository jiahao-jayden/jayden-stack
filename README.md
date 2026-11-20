# jayden-stack

面向 **Cursor** 与 **Codex** 双宿主的完整中文 Agent 工程工作流插件（基于 Lauren Tan 的 [pstack](https://github.com/cursor/plugins/tree/main/pstack) 与 [open-pstack](https://github.com/ericlitman/open-pstack) 深度翻译、去 Cursor 内部耦合，并融合个人工程铁律与验证闭环）。

核心主张：**欲速则先深挖。写更少但更稳的代码；每一项结论靠真实运行证据说话，每一项任务一路收到约定的终态。**

## 整体结构

### 1. 日常最常用的 5 个顶层入口

日常 90% 的场景只需叫这 5 个入口，背后全部落到完整 22 个中文 Playbook 与 15 个高密度专项 Skill：

| 入口 | 定位 | 背后路由的 Playbook / 能力 |
| --- | --- | --- |
| **`/jstack`** | 总入口（全自动路由） | 全部 22 个中文 Playbook + 23 条工程原则 + 6 条个人铁律 + `worker.md` 穿透 |
| **`/explore`** | 探索、诊断、原型与评审（不动正式代码） | `investigation` / `runtime-forensics` / `trace-forensics` / `prototype` / `how` / `why` / `interrogate` / `blast-radius` |
| **`/build`** | 正式开发、重构与性能自证 | `bug-fix` / `feature` / `refactoring` / `perf-issue` / `hillclimb` / `visual-parity` / `multi-phase-plan` / `figure-it-out` |
| **`/ship`** | 一路收到约定终态 | `opening-a-pr` / `babysit` / `shipping` / `autonomous-run` / `autopilot-full` / `autopilot-stack` / `no-comments` / `correct` |
| **`/handoff`** | 跨会话 / Cursor ↔ Codex 交接与清理 | `pause-safely` / `session-pickup` / `worktree-cleanup` |

### 2. `/jstack` 内置的完整 4 大类 22 个中文 Playbook

- **调查、修复与性能（6）**：`investigation`、`bug-fix`、`perf-issue`、`hillclimb`、`runtime-forensics`、`trace-forensics`
- **开发、设计与评估（6）**：`feature`、`refactoring`、`prototype`、`visual-parity`、`authoring-a-skill`、`eval`
- **PR、发布与长任务（6）**：`opening-a-pr`、`babysit`、`shipping`、`autonomous-run`、`autopilot-full`、`autopilot-stack`
- **跨阶段、交接与维护（4）**：`session-pickup`、`pause-safely`、`multi-phase-plan`、`worktree-cleanup`

### 3. 15 个高密度专项 Skill（Playbook 按需调用，也可单独直呼）

| Skill | 作用 |
| --- | --- |
| `/architect` | 写代码前先画 2 套接口/边界方案对比选型，锁定唯一状态归属 |
| `/arena` | 多模型隔离 worktree 并行赛马 → 跨厂商盲评裁判按 rubric 打分 → 手工嫁接精华 |
| `/interrogate` | 跨厂商对抗式只读破坏审查，**拿不出运行复现证明的发现一律丢弃** |
| `/blast-radius` | 追查 diff 之外 grep 看不到的隐蔽破坏，按 5 级确定性阶梯跑真实代码证明 |
| `/swarm` | 并发分片覆盖 / 同题赛跑 / 独立验证，强制回填 SHA 与采样方法 |
| `/figure-it-out` | 超大跨模块任务专用：先设计可审计阶段剧本 + 建基线脚手架 + 科学实验环 |
| `/how` | 只读梳理模块架构、运行时数据流与分层归属 |
| `/why` | 从 git 历史、PR 与决策记录还原设计动机，绝不靠现代码脑补 |
| `/tdd` | 低成本先红后绿回归测试流程（不适用时果断跳过，不造脆测试） |
| `/benchmark-checklist` | 性能数字七问防伪核对（限制器、调优、物理上限、错误数、可重复性、端到端占比、工作是否真发生） |
| `/show-me-your-work` | 可审计 TSV 决策轨迹日志 + transcript 自审 + 跨厂商轨迹隐患扫描 |
| `/correct` | 按 `架构 > 类型 > Lint > 测试 > 文档` 永久根除反复踩过的同类错，并用历史真错验证 |
| `/no-comments` | 派出 `comment-sicko` 清扫废话注释与遮丑 workaround，并按根因修复 `MUST KILL` 符号 |
| `/verify-setup` | 为任意项目首次生成或定期校准项目专属 `verify-<app>` skill 与 Feature Map |
| `/setup` | 一键体检并初始化本机 Cursor 与 Codex 双宿主子代理及跨厂商评审通道 |

### 4. 双宿主原生支持

- **Cursor**：通过 `.cursor-plugin/marketplace.json` 加载全部 20 个 Skills + 4 个原生 Agents（`scout` / `worker` / `reviewer` / `comment-sicko`），原生 `Task` 支持跨厂商模型。
- **Codex**：通过 `.agents/plugins/marketplace.json` 加载同一套 20 个 Skills；运行 `/setup` 生成 `~/.codex/agents/*.toml`，并通过自带无依赖脚本 `plugins/jstack/scripts/run-external.mjs` 调本机 `claude` CLI 完成跨厂商只读评审（失败显式返回 `dropout`，绝不静默降级）。

## 开源协议

MIT License。致谢 Lauren Tan 的 `pstack` 与 `open-pstack` 社区，详见 [LICENSE](./LICENSE)。
