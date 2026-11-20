---
name: explore
description: 探索、诊断、原型与只读评审的一键入口（绝不修改正式业务代码）。按场景自动路由到 investigation / runtime-forensics / trace-forensics / prototype / how / why / interrogate / blast-radius。
---

# explore（探索、诊断、原型与评审）

**硬边界：绝不修改正式业务代码。** 原型仅允许建在隔离临时目录，且必须明确标明用完即扔。

先按本次探索目的，**选定对应 Playbook 或专项 Skill**，把步骤放进 todo 后推进，并始终遵守 [`../jstack/SKILL.md`](../jstack/SKILL.md) 的六条铁律：

| 探索场景 | 读取并执行 |
| --- | --- |
| 只读调查、排查根因、方案权衡 | [`../jstack/playbooks/investigation.md`](../jstack/playbooks/investigation.md) |
| 活进程现场采样 / 插桩诊断（CPU、内存、卡顿） | [`../jstack/playbooks/runtime-forensics.md`](../jstack/playbooks/runtime-forensics.md) |
| 分析已有 profile / heap / spindump 采样文件 | [`../jstack/playbooks/trace-forensics.md`](../jstack/playbooks/trace-forensics.md) |
| 隔离目录做一次性原型拍板布局/交互/时序分叉 | [`../jstack/playbooks/prototype.md`](../jstack/playbooks/prototype.md) |
| 梳理模块架构、运行时数据流与分层归属 | [`../how/SKILL.md`](../how/SKILL.md) |
| 查 git / PR 历史追溯“为什么这样写” | [`../why/SKILL.md`](../why/SKILL.md) |
| 跨厂商对抗式破坏审查（无复现证据一律丢弃） | [`../interrogate/SKILL.md`](../interrogate/SKILL.md) |
| 查 diff 之外 grep 看不到的爆炸半径 + 5 级证明 | [`../blast-radius/SKILL.md`](../blast-radius/SKILL.md) |

执行要点：
- 结论优先依据真实日志、请求/响应、采样文件与 git 记录，每条明确标注 `实测`、`推断` 或 `未验证`。
- 跨厂商评审前，若仓库 base 分支存在 `REVIEW.md`，先用 `git show <base>:REVIEW.md` 读取作为基准。
- 探索结束只交付带实证的真相、决策或评审清单；需要动手改正式代码时再转 `/build`。
