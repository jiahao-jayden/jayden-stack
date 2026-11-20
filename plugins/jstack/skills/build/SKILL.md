---
name: build
description: 正式代码开发与端到端自证。用于修 Bug、做新功能、重构或性能优化，强制覆盖多入口清单、正反实测与三轮熔断。
---

# build

按本次开发任务类型，**只选一个**对应 Playbook，把步骤写入 todo 后逐项推进：

- 修 Bug / 排查并修复异常 → 读取 [`../jstack/playbooks/bug-fix.md`](../jstack/playbooks/bug-fix.md)
- 新功能 / 交互改动 / 补能力 → 读取 [`../jstack/playbooks/feature.md`](../jstack/playbooks/feature.md)
- 不改外部行为的结构整理 → 读取 [`../jstack/playbooks/refactoring.md`](../jstack/playbooks/refactoring.md)
- 性能耗时 / 卡顿 / 内存 / 体积优化 → 读取 [`../jstack/playbooks/perf-issue.md`](../jstack/playbooks/perf-issue.md)

始终遵守 [`../jstack/SKILL.md`](../jstack/SKILL.md) 的六条铁律与 [`../jstack/references/principles.md`](../jstack/references/principles.md) 的工程原则：
- 不留向后兼容层，先减后加；跨模块先定好接口和状态归属。
- 开工先列清用户点名的全部端与入口；UI 改动必测正反状态（展开+收起、遮挡检查）。
- 严守用户指定的设备与运行方式（热更新、真机、禁模拟器等）；项目有 `verify-*` skill 时优先用它验收。
- 同一问题连续 3 轮修不过立刻停下，带失败证据找人讨论。
- 如需子代理并行或跨厂商评审，按 [`../jstack/references/host-dispatch.md`](../jstack/references/host-dispatch.md) 执行。
