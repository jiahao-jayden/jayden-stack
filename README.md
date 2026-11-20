# jayden-stack

面向 **Cursor** 与 **Codex** 双宿主的极简中文 Agent 工程工作流插件（基于 Lauren Tan 的 [pstack](https://github.com/cursor/plugins/tree/main/pstack) 与 [open-pstack](https://github.com/ericlitman/open-pstack) 精简改写）。

核心主张：**先深挖后动手，写更少但更稳的代码，用真实运行证据自证，并一路收到约定的终态。**

## 包含哪些 Skill

| Skill | 定位 | 什么时候用 |
| --- | --- | --- |
| `/jstack` | **总入口与 Playbook 路由** | 不想自己选流程时直接叫它，自动路由到对应剧本 |
| `/explore` | **不动正式代码** | 查根因、读懂模块、做一次性原型对比方案、跨厂商对抗评审 |
| `/build` | **正式开发与自证** | 修 Bug、做新功能、重构、性能优化，含端到端真实环境验证 |
| `/ship` | **收口到终态** | 自清冗余代码、拆干净提交、提 PR、盯 CI/评审、合并或发版、沉淀防错规则 |
| `/handoff` | **跨会话 / 跨工具交接** | 暂停保存干净交接点，或在 Cursor / Codex 间无缝接手 |
| `/verify-setup` | **项目验证闭环工具** | 为当前仓库生成或校准项目专属 `verify-<app>` skill 与 Feature Map |
| `/setup` | **本机环境初始化** | 一键检查与配置本机 Cursor 与 Codex 子代理、跨厂商评审通道 |

## 设计取舍

- **一份剧本，多端复用**：全部流程剧本集中在 `plugins/jstack/skills/jstack/playbooks/`，子 skill 只锁死职责边界并引用剧本，不复制粘贴。
- **内置五条个人高频铁律**：收到约定的终态、真实环境正反实测、结论带现场证据、多入口逐项核对、严守指定的设备与运行方式；外加「连续 3 轮修不过立刻停下」的熔断机制。
- **双宿主各自发挥强项**：
  - **Cursor**：通过原生 `Task` 工具与 `agents/{scout,worker,reviewer}.md` 实现快侦察 / 同模开发 / 跨厂商评审。
  - **Codex**：通过原生 `spawn_agent` + `~/.codex/agents/*.toml` 并行侦察与开发，并用 `scripts/run-external.mjs` 调用本机 `claude` CLI 完成跨厂商只读评审（失败显式上报，绝不静默降级）。

## 安装方式

### Cursor

在 Cursor 插件市场添加本地路径或 GitHub 仓库 `jiahao-jayden/jayden-stack`，安装插件 `jstack`，然后运行一次 `/setup`。

### Codex

在 Codex 插件市场接入本仓库（`.agents/plugins/marketplace.json`）并安装 `jstack`，或将本仓库克隆到本地后，在 Codex 中运行 `/setup` 完成 `~/.codex/config.toml` 与 `~/.codex/agents/` 初始化。

## 开源协议

MIT License。致谢 Lauren Tan 的 `pstack` 与 `open-pstack` 社区，详见 [LICENSE](./LICENSE)。
