# 双宿主调度约定（Cursor 与 Codex）

主会话负责定范围、管 todolist、把控验收和最终收口；繁重读代码、解析大日志、独立模块编码、对抗审查与并行赛马按角色分派，保护主上下文干净。

## 一、四个固定角色

| 角色 | 职责 | 权限 | 模型策略 |
| --- | --- | --- | --- |
| `scout` | 只读侦察、查调用链、读 git 历史与现场日志、解析大 trace/transcript | 只读 | 快模型（适合并发多路扫线索） |
| `worker` | 按明确接口和文件边界实现代码、补测试、跑验证 | 读写 | 继承当前主力开发模型（难题可提档，机械活可降档） |
| `reviewer` | 对抗式挑刺、找边界反例、`arena` 裁判、`show-me-your-work` 轨迹审计 | 只读 | **跨厂商强模型**（绝不与干活模型同厂商） |
| `comment-sicko` | 专门扫清无意义注释、死代码注释与用注释掩盖的 workaround，标出 `MUST KILL` 重构点 | 只改注释/不改业务代码 | 轻量或标准模型 |

> 跨厂商规则：当前主会话是 OpenAI / GPT 系时，`reviewer` / 裁判优先选 Anthropic / Claude 系或 Gemini 系；主会话是 Claude / Gemini 系时，优先选 OpenAI / GPT 系或其他不同厂商强模型。

## 二、所有子代理的统一铁律

1. **首行穿透 `worker.md`**：每个派生任务书的第一行必须是：
   `先完整阅读 <jstack目录绝对路径>/worker.md，再做任何事。`
2. **鲜活子代理优先**：修补轮、重试轮、下一个独立切片默认新开子代理（附：原始目标 + 后续纠偏 + 上一轮报告与分支），不继续拖被报错污染的旧对话。仅当旧子代理持有难迁移的现场状态（未提交 worktree、运行中的调试进程）时才 resume。
3. **文件指针优先于全文粘贴**：给子代理路径和范围，不把几千行源码塞进主对话上下文。
4. **主会话负全责**：所有子代理的 diff 和发现，主会话必须亲自核查或重跑关键命令后再采纳，不盲信自报通过。

## 三、在 Cursor 中派发

直接使用原生 `Task` 工具（优先 `run_in_background: true` 并发多个独立任务）：

- **侦察**：`subagent_type: "explore"` 或插件注册的 `scout`（`readonly: true`）。
- **实现**：`subagent_type: "generalPurpose"` 或插件注册的 `worker`；给出精确文件边界、数据形状、成功判据与必跑验证命令。
- **跨厂商评审 / 裁判**：`subagent_type: "generalPurpose"` 或 `reviewer`，`readonly: true`，在 `model` 参数显式传入与当前主模型不同厂商的强模型（从本会话可用子代理模型列表选择）。若 `Task` 拒绝某个 model slug，不再重试同一个 slug，改用同家族最近的有效 slug 并向用户说明。
- **去注释清理**：`subagent_type: "comment-sicko"`（由 [no-comments](../../ship/references/no-comments.md) 调用）。
- 若仓库存在 `REVIEW.md`，主会话先用 `git show <base>:REVIEW.md` 读出原文一并交给 `reviewer`。

## 四、在 Codex 中派发

- **侦察与实现**：使用原生 `spawn_agent`，调用由 `/setup` 生成到项目 `.codex/agents/`（或全局 `~/.codex/agents/`）的角色配置（`scout`、`worker`、`reviewer`）。多路并行写代码时，每个 `worker` 必须分配互不重叠的目录或独立 git worktree。
- **跨厂商只读评审 / 裁判 / 轨迹审计**：Codex 原生子代理跑在 OpenAI 生态内，需要真正的跨厂商视角时，调用本插件自带脚本驱动本机 `claude` CLI：

```bash
node <jstack-skill-dir>/scripts/run-external.mjs \
  --cwd "<repo-root>" \
  --prompt-file "<review-prompt.md>" \
  --context-file "<diff-or-plan.txt>" \
  --effort high \
  --timeout 300
```

检查返回的 JSON receipt：
- `status === "ok"`：取 `result`，主会话逐条对照源码或复现命令核实后再行动。
- `status === "dropout"`：向用户如实回报跨厂商评审未能运行及精确 `reason`（例如未登录、超时、缺 CLI），**绝不静默假装已完成跨厂商评审**；由用户决定修复通道或改用同厂商 `reviewer`。
