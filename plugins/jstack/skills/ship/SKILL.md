---
name: ship
description: 收口交付到约定终态的一键入口。内置 no-comments / correct / retro / show-me-your-work 四份深度手册及日志脚本，并路由 opening-a-pr / babysit / shipping / autonomous-run / autopilot-full / autopilot-stack 剧本。
---

# ship（收口交付到约定终态）

先明确用户本次约定的终态（本地提交 / 推送 / 开 PR / 盯绿 CI / 独立验证合入 `main` / 批量 PR / 发版），选定对应 Playbook 并将步骤写入 todo，**未达终态并读回核验前绝不宣称已完成**：

| 收口场景 | 读取并执行 |
| --- | --- |
| 自清冗余代码、整理提交并准备 Ready PR | [`../jstack/playbooks/opening-a-pr.md`](../jstack/playbooks/opening-a-pr.md) |
| 跟进 PR 冲突、评论与 CI 直到可合并 | [`../jstack/playbooks/babysit.md`](../jstack/playbooks/babysit.md) |
| 独立验证并自底向上合入主干（含 PR 栈） | [`../jstack/playbooks/shipping.md`](../jstack/playbooks/shipping.md) |
| 给定退出条件持续推进长任务直到达标 | [`../jstack/playbooks/autonomous-run.md`](../jstack/playbooks/autonomous-run.md) |
| 并行把一批独立 PR 从开发、蜂群验证做到合并 | [`../jstack/playbooks/autopilot-full.md`](../jstack/playbooks/autopilot-full.md) |
| 并行做完并验证一批 PR（或 PR 栈）后交接不合并 | [`../jstack/playbooks/autopilot-stack.md`](../jstack/playbooks/autopilot-stack.md) |

## 内置收口与审计参考手册（按需触发读取）

- **清理废话注释、升格隐性约束为 ADR 与处理 `MUST KILL`**：[`references/no-comments.md`](references/no-comments.md)
- **收尾复盘（扫过程 transcript + 成品 diff）与棘轮固化**：[`references/retro.md`](references/retro.md)
- **把反复踩过的同类坑固化为架构/类型/Lint/测试防线**：[`references/correct.md`](references/correct.md)
- **可审计 TSV 决策轨迹日志 + 跨厂商审计**：[`references/show-me-your-work.md`](references/show-me-your-work.md)（配 `scripts/log.sh`）
- 收口前按需核对 [`../jstack/references/adr.md`](../jstack/references/adr.md) 补齐/收口 ADR；常规体检过 [`../explore/references/code-review.md`](../explore/references/code-review.md)，中高风险合并前过 [`../explore/references/interrogate.md`](../explore/references/interrogate.md) 或 [`../explore/references/blast-radius.md`](../explore/references/blast-radius.md)。
