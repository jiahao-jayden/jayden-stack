---
name: handoff
description: 安全暂停保存干净交接点，或跨会话 / 跨工具（Cursor 与 Codex 之间）无缝接手继续推进任务。
---

# handoff

立即读取并执行 [`../jstack/playbooks/handoff.md`](../jstack/playbooks/handoff.md)。

- **要暂停 / 切工具**：先清理无用进程、收好 commit（或干净的 `wip:` 提交），再输出一屏以内的高密度交接块（目标与终态、分支与 commit SHA、已验证证据、下一步与已踩坑的死路）。
- **要接手继续**：先用 `git status` 和 `git log` 核实仓库现场，信任已有 commit 和已验证证据不从头重做，立刻把剩余步骤放进 todo 并继续直奔终态。
