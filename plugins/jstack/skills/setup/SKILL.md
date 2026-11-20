---
name: setup
description: 一键检查并初始化本机 Cursor 与 Codex 的 jstack 运行环境（Codex 子代理配置、multi_agent 开关、以及本机 claude 跨厂商只读评审通道）。
disable-model-invocation: true
---

# setup

负责把当前机器上的 **Cursor** 与 **Codex** 宿主环境配到可直接按 `jstack` 满血运转的状态。任何改动本机 `~/.codex/` 或 `~/.cursor/` 配置文件前，先向用户展示拟改内容摘要，获准后再写。

## 1. 先做只读环境体检

并行检查以下项目，整理成一张简明现状表：

1. **当前宿主与插件位置**：确认当前是 Cursor 还是 Codex，定位 `jstack` 插件目录（确认 `scripts/run-external.mjs` 与 `agents/` 存在）。
2. **Cursor 环境**：检查 `~/.cursor/agents/`、以及插件自带的 `agents/{scout,worker,reviewer}.md` 是否已可见。
3. **Codex 环境**：
   - 检查 `codex --version`；
   - 读取 `~/.codex/config.toml`，确认是否存在 `[features] multi_agent = true` 与 `[agents]` 配置；
   - 检查 `~/.codex/agents/` 下是否已有 `scout.toml`、`worker.toml`、`reviewer.toml`。
4. **跨厂商评审通道（供 Codex 使用）**：
   - 检查 `node --version`（需 Node 20+）与 `which claude && claude --version`；
   - 检查 `claude` 是否已完成本机登录鉴权。

## 2. 按需补齐 Codex 配置（需用户确认）

如果用户使用 Codex，向用户确认后执行以下两步（不覆盖用户已有的模型与个人偏好配置）：

1. 在 `~/.codex/config.toml` 中按需补充（已存在则保留原值不重复添加）：
   - `[features]` 下开启 `multi_agent = true`
   - `[agents]` 下设置 `max_depth = 1`
2. 在 `~/.codex/agents/` 生成三个精简角色配置：
   - **`scout.toml`**：`sandbox_mode = "read-only"`，定位只读侦察、查代码链与现场日志、每条结论标注 `实测/推断/未验证`。
   - **`worker.toml`**：`sandbox_mode = "workspace-write"`，定位按清晰边界做最小实现与真实验证、不留兼容层、三轮熔断。
   - **`reviewer.toml`**：`sandbox_mode = "read-only"`，定位对抗式只读评审、按四类输出、只报有证据的真实问题。

## 3. 实测验证（必须真跑，不靠干看）

1. **验证 `run-external.mjs` 跨厂商评审通道**（如果本机装了 `claude`）：
   运行一次最小冒烟调用（例如让 `claude` 回复 `OK`），检查返回的 JSON receipt 是否为 `"status": "ok"`；若返回 `"status": "dropout"`，把精确 `reason`（如未登录 `claude auth login` 或网络受限）报给用户。
2. **验证 Codex 配置语法**：确认 `~/.codex/config.toml` 与 `~/.codex/agents/*.toml` 格式合法无报错。
3. **汇总验收报告**：列出 Cursor 端状态、Codex 端状态、跨厂商评审通道状态，以及下一步怎么在项目里开始用 `/jstack`（或运行 `/verify-setup` 为当前仓库建项目验证闭环）。
