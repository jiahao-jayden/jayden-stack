---
name: build
description: 正式代码开发与端到端自证的一键入口。按场景路由到 bug-fix / feature / refactoring / perf-issue / hillclimb / visual-parity / multi-phase-plan / figure-it-out，并调用 architect / arena / tdd 等专项能力。
---

# build（正式开发、重构与性能自证）

按本次研发任务类型，**选定唯一主 Playbook** 并把步骤写入 todolist 逐项推进：

| 开发场景 | 读取并执行 |
| --- | --- |
| 复现、二分追根因并修复缺陷 | [`../jstack/playbooks/bug-fix.md`](../jstack/playbooks/bug-fix.md) |
| 实现新功能、新交互或端到端能力 | [`../jstack/playbooks/feature.md`](../jstack/playbooks/feature.md) |
| 锚定行为契约、整理旧代码结构 | [`../jstack/playbooks/refactoring.md`](../jstack/playbooks/refactoring.md) |
| 修复一次明确的性能问题 | [`../jstack/playbooks/perf-issue.md`](../jstack/playbooks/perf-issue.md) |
| 围绕单一可量化指标反复实验爬坡 | [`../jstack/playbooks/hillclimb.md`](../jstack/playbooks/hillclimb.md) |
| 像素级外观一致性迁移与图像 diff 校验 | [`../jstack/playbooks/visual-parity.md`](../jstack/playbooks/visual-parity.md) |
| 大改动先出可逐框审计的正式多阶段计划（不写代码） | [`../jstack/playbooks/multi-phase-plan.md`](../jstack/playbooks/multi-phase-plan.md) |
| 超大跨模块任务：先设计专属工作流 + 科学实验环 | [`../figure-it-out/SKILL.md`](../figure-it-out/SKILL.md) |

始终遵守 [`../jstack/SKILL.md`](../jstack/SKILL.md) 的六条铁律与 [`../jstack/references/principles.md`](../jstack/references/principles.md)：
- 跨边界先跑 [`../architect/SKILL.md`](../architect/SKILL.md)，多种有效形状难取舍时跑 [`../arena/SKILL.md`](../arena/SKILL.md)，不留向后兼容层、先减后加。
- 开工先列清用户点名的全部端与入口；UI 改动必测正反状态（展开+收起、浮层不遮挡按钮）；严守指定设备与运行方式（热更新/真机/禁模拟器）。
- 同一前提连挂 2 次先「质疑前提」，同一问题累计 3 轮修不过立刻熔断找人。
- 子代理派发首行必读 `worker.md`，见 [`../jstack/references/host-dispatch.md`](../jstack/references/host-dispatch.md)。
