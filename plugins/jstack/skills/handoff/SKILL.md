---
name: handoff
description: 跨会话、跨工具（Cursor ↔ Codex）交接与工作区维护的一键入口。路由到 pause-safely / session-pickup / worktree-cleanup，保留干净锚点、避免重做已完成工作。
---

# handoff（暂停交接、无缝接手与工作区清理）

按当前需要选择对应 Playbook 并执行：

| 交接与维护场景 | 读取并执行 |
| --- | --- |
| 安全停下并留下冷启动可接手的检查点（含 `wip:` 提交与续接块） | [`../jstack/playbooks/pause-safely.md`](../jstack/playbooks/pause-safely.md) |
| 接手上一个会话或 Cursor ↔ Codex 留下的进度继续推进 | [`../jstack/playbooks/session-pickup.md`](../jstack/playbooks/session-pickup.md) |
| 审计并安全清理旧 git worktree、缓存与废弃构建产物 | [`../jstack/playbooks/worktree-cleanup.md`](../jstack/playbooks/worktree-cleanup.md) |

核心原则：
- **暂停时**：不留无人看管的临时进程，不触发不可逆写入，交接块一屏讲清目标终态、分支/SHA、实测证据、下一步与已踩过的死路。
- **接手时**：先核 `git status` 与 `git log`，信任前序已验证进度不推倒重做，直接把剩余项放进 todo 奔向终态。
