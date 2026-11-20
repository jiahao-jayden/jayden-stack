---
name: ship
description: 收口交付到约定终态的一键入口。按目标路由到 opening-a-pr / babysit / shipping / autopilot-full / autopilot-stack / autonomous-run，并串联 no-comments 与 correct 沉淀防错。
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

收口配套能力：
- 提交前如注释冗余或藏有 workaround，跑 [`../no-comments/SKILL.md`](../no-comments/SKILL.md)；
- 中高风险合并前可先过 [`../interrogate/SKILL.md`](../interrogate/SKILL.md) 或 [`../blast-radius/SKILL.md`](../blast-radius/SKILL.md)；
- 若本次重复踩到了同类坑（≥2 次），收尾按 [`../correct/SKILL.md`](../correct/SKILL.md) 建议升级为架构/类型/Lint/测试级永久防线。
