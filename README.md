# jayden-stack

面向 **Cursor** 与 **Codex** 双宿主的完整中文 Agent 工程工作流插件（基于 Lauren Tan 的 [pstack](https://github.com/cursor/plugins/tree/main/pstack) 与 [open-pstack](https://github.com/ericlitman/open-pstack) 深度翻译、去 Cursor 内部耦合，并融合个人工程铁律与验证闭环）。

核心主张：**欲速则先深挖。写更少但更稳的代码；每一项结论靠真实运行证据说话，每一项任务一路收到约定的终态。**

## 设计原则：顶层极简，底层厚实

对外只暴露 **7 个清晰顶层 Skill**（不污染斜杠命令面板和上下文 catalog），但把 `pstack` 的全部 **22 个完整 Playbook** 与 **13 份专项重武器手册**一字不减地收纳进各主 Skill 的 `playbooks/` 与 `references/` 目录中按需加载：

| 顶层 Skill | 定位 | 内部收纳的完整 Playbook / References |
| --- | --- | --- |
| **`/jstack`** | 总入口（全自动路由） | **22 个中文 Playbook** + `worker.md` 穿透规则 + `references/{principles,host-dispatch,bugbot-triage}.md` + `scripts/worktree-audit.sh` |
| **`/explore`** | 探索、诊断、原型与评审（不动正式代码） | `references/{how,why,interrogate,blast-radius}.md`；路由 `investigation` / `runtime-forensics` / `trace-forensics` / `prototype` |
| **`/build`** | 正式开发、重构与性能自证 | `references/{architect,arena,swarm,figure-it-out,tdd,benchmark-checklist}.md`；路由 `bug-fix` / `feature` / `refactoring` / `perf-issue` / `hillclimb` / `visual-parity` / `multi-phase-plan` |
| **`/ship`** | 一路收到约定终态 | `references/{no-comments,correct,show-me-your-work,decision-log-template.tsv}` + `scripts/log.sh`；路由 `opening-a-pr` / `babysit` / `shipping` / `autonomous-run` / `autopilot-full` / `autopilot-stack` |
| **`/handoff`** | 跨会话 / Cursor ↔ Codex 交接与清理 | 路由 `pause-safely` / `session-pickup` / `worktree-cleanup` |
| **`/verify-setup`** | 项目验证闭环工具 | 为任意仓库首次生成或定期校准项目专属 `verify-<app>` skill 与 Feature Map |
| **`/setup`** | 本机环境初始化 | 一键体检并初始化本机 Cursor 与 Codex 双宿主子代理及跨厂商评审通道 |

## 22 个中文 Playbook 全览（`plugins/jstack/skills/jstack/playbooks/`）

- **调查、修复与性能（6）**：`investigation`、`bug-fix`、`perf-issue`、`hillclimb`、`runtime-forensics`、`trace-forensics`
- **开发、设计与评估（6）**：`feature`、`refactoring`、`prototype`、`visual-parity`、`authoring-a-skill`、`eval`
- **PR、发布与长任务（6）**：`opening-a-pr`、`babysit`、`shipping`、`autonomous-run`、`autopilot-full`、`autopilot-stack`
- **跨阶段、交接与维护（4）**：`session-pickup`、`pause-safely`、`multi-phase-plan`、`worktree-cleanup`

## 双宿主原生支持

- **Cursor**：通过 `.cursor-plugin/marketplace.json` 加载 7 个顶层 Skills + 4 个原生 Agents（`scout` / `worker` / `reviewer` / `comment-sicko`），原生 `Task` 支持跨厂商模型。
- **Codex**：通过 `.agents/plugins/marketplace.json` 加载同一套 7 个顶层 Skills；运行 `/setup` 生成 `~/.codex/agents/*.toml`，并通过自带无依赖脚本 `plugins/jstack/scripts/run-external.mjs` 调本机 `claude` CLI 完成跨厂商只读评审（失败显式返回 `dropout`，绝不静默降级）。

## 开源协议

MIT License。致谢 Lauren Tan 的 `pstack` 与 `open-pstack` 社区，详见 [LICENSE](./LICENSE)。
