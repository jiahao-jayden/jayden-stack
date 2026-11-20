# 双宿主调度约定（Cursor 与 Codex）

主会话负责定范围、做决策、把控验收和最终收口；具体侦察、边界清晰的实现、对抗式只读评审按角色分派，避免主上下文被大量源码和报错撑爆。

## 三个固定角色

| 角色 | 职责 | 权限 | 模型策略 |
| --- | --- | --- | --- |
| `scout` | 查代码路径、追踪调用链、读 git 历史与现场日志 | 只读 | 快模型（省时间、并发扫多条线索） |
| `worker` | 按明确接口与范围写代码、补测试、跑验证 | 读写 | 继承当前主力开发模型 |
| `reviewer` | 对抗式挑刺、找边界反例、查漏项与隐患 | 只读 | **跨厂商强模型**（避免同源盲区） |

> 跨厂商原则：当前主会话是 OpenAI / GPT 系时，`reviewer` 优先选 Anthropic / Claude 系；当前主会话是 Claude / Gemini 系时，`reviewer` 优先选 OpenAI / GPT 系或其他不同厂商强模型。

## 在 Cursor 中派发

直接使用原生 `Task` 工具：

- **侦察**：`subagent_type: "explore"`（或使用插件注册的 `scout`），只读，可并发派发多路互不重叠的调查线索。
- **实现**：`subagent_type: "generalPurpose"`（或 `worker`），给清文件路径、目标契约、禁止触碰的范围和必须跑的验证命令。
- **跨厂商评审**：`subagent_type: "generalPurpose"`（或 `reviewer`），`readonly: true`，并在 `model` 参数中显式指定与当前主模型不同厂商的强模型（可从当前会话可用子代理模型列表中选择）。
- 若项目本地有 `REVIEW.md`，主会话先用 `git show <base>:REVIEW.md` 读出原文，原样附给 `reviewer`。

## 在 Codex 中派发

- **侦察与实现**：使用原生 `spawn_agent`，引用由 `/setup` 写入 `~/.codex/agents/` 的角色配置（`scout`、`worker`、`reviewer`）。并发派发时，每个 `worker` 必须分配互不重叠的文件边界。
- **跨厂商只读评审**：Codex 原生子代理跑在 OpenAI 生态内，需要真正跨厂商视角时，调用本插件自带脚本触发本机 `claude` CLI：

```bash
node <jstack-plugin-dir>/scripts/run-external.mjs \
  --cwd "<repo-root>" \
  --prompt-file "<review-prompt.md>" \
  --context-file "<diff-or-plan.txt>" \
  --effort high \
  --timeout 300
```

读取脚本输出的 JSON receipt：
- `status === "ok"`：提取 `result` 并逐条用真实代码/命令复核后再采纳。
- `status === "dropout"`：向用户如实报告跨厂商评审未完成及 `reason`（例如未登录、超时、缺 CLI），**绝不静默假装已做跨厂商评审**；由用户决定重试或改用同厂商 `reviewer` 子代理。

## 所有派发的通用护栏

- 给子代理的 brief 必须自带上下文（文件路径、目标、约束、验证方式），不要假设它能看到主会话历史。
- 评审子代理返回的每一条发现，主会话必须自己对照源码或重跑复现命令核实后再动手改，不盲信二手结论。
- 子代理遇到环境阻塞或三轮失败时立即回报，不在后台无限空转。
