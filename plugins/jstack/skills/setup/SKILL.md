---
name: setup
description: 一键检查并初始化当前项目的 Cursor 与 Codex jstack 运行环境（默认项目级 .codex/ 与 .cursor/agents/，不污染全局，并验证本机 claude 跨厂商只读评审通道）。
disable-model-invocation: true
---

# setup

负责把 **Cursor** 与 **Codex** 的运行环境配到可直接按 `jstack` 满血运转的状态。

**作用域铁律（默认项目级，不碰全局）**：
- 默认只写入**当前项目目录**（`.codex/config.toml`、`.codex/agents/*.toml`、`.cursor/agents/*.md`），既不污染全局配置，又能随仓库提交让团队成员 `git pull` 即用。
- **绝不默认修改 `~/.codex/` 或 `~/.cursor/`**；仅当用户明确要求“写入全局供所有仓库共用”时才切换为全局作用域。
- 写入任何配置文件前，先向用户展示拟改路径与摘要，获准后再写。

## 1. 先做只读环境体检

并行检查以下项目，整理成一张简明现状表：

1. **当前宿主与技能位置**：确认当前是 Cursor 还是 Codex，定位 `jstack` skill 目录（确认 `jstack/scripts/run-external.mjs` 存在）。
2. **Cursor 环境**：优先检查当前项目 `.cursor/agents/` 下是否已有 `scout.md`、`worker.md`、`reviewer.md`、`comment-sicko.md`（若全局 `~/.cursor/agents/` 已有也顺带标明，仅作参考）。
3. **Codex 环境**：
   - 检查 `codex --version`；
   - 检查当前项目 `.codex/config.toml`（若存在）是否已声明 `[agents.scout]`、`[agents.worker]`、`[agents.reviewer]` 及其相对 `config_file`；
   - 检查当前项目 `.codex/agents/` 下是否已有 `scout.toml`、`worker.toml`、`reviewer.toml`；
   - （注：现代 Codex 默认启用 `agents.enabled = true`，`features.multi_agent` 已废弃，不再写入过时开关。）
4. **跨厂商评审通道（供 Codex 使用）**：
   - 检查 `node --version`（需 Node 20+）与 `which claude && claude --version`；
   - 检查 `claude` 是否已完成本机登录鉴权。

## 2. 按需补齐项目级配置（需用户确认）

向用户展示拟写目录（默认项目级 `.codex/`，可选切换全局）并获准后执行，**不覆盖用户已有的模型与个人偏好配置**：

1. **在当前项目 `.codex/config.toml` 中按需补充角色声明**（已存在则保留原值不重复添加；`config_file` 相对路径会自动相对 `.codex/config.toml` 解析）：
   ```toml
   [agents.scout]
   description = "只读侦察子代理：查调用链、git 历史、现场日志与根因线索"
   config_file = "agents/scout.toml"

   [agents.worker]
   description = "正式实现与自证子代理：按明确边界做最小改动并实测验证"
   config_file = "agents/worker.toml"

   [agents.reviewer]
   description = "对抗式只读评审子代理：挑刺、找盲区与边界反例"
   config_file = "agents/reviewer.toml"
   ```
2. **在当前项目 `.codex/agents/` 生成三个精简角色配置**：
   - **`scout.toml`**：`sandbox_mode = "read-only"`，定位只读侦察、查代码链与现场日志、每条结论标注 `实测/推断/未验证`。
   - **`worker.toml`**：`sandbox_mode = "workspace-write"`，定位按清晰边界做最小实现与真实验证、不留兼容层、三轮熔断。
   - **`reviewer.toml`**：`sandbox_mode = "read-only"`，定位对抗式只读评审、只报有精确证据的真实问题。
3. **按需补齐 Cursor 子代理**：若项目 `.cursor/agents/` 缺少 `scout.md` / `worker.md` / `reviewer.md` / `comment-sicko.md` 且用户希望补齐，写入项目 `.cursor/agents/`（不写全局）。

## 3. 实测验证（必须真跑，不靠干看）

1. **验证 `run-external.mjs` 跨厂商评审通道**（如果本机装了 `claude`）：
   运行一次最小冒烟调用（例如让 `claude` 回复 `OK`），检查返回的 JSON receipt 是否为 `"status": "ok"`；若返回 `"status": "dropout"`，把精确 `reason`（如未登录 `claude auth login` 或网络受限）报给用户。
2. **验证 Codex 配置语法**：解析确认项目 `.codex/config.toml` 与 `.codex/agents/*.toml` 为合法 TOML，且 `config_file` 指向的文件全部真实存在。
3. **汇总验收报告**：列出 Cursor 端状态、Codex 端状态（项目级）、跨厂商评审通道状态，以及下一步怎么在项目里开始用 `/jstack`（或运行 `/verify-setup` 为当前仓库建项目验证闭环）。
