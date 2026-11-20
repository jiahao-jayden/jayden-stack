---
name: explore
description: 只读探索、查根因、读架构/历史、做一次性隔离原型对比方案、或跨厂商对抗评审。绝不修改正式业务代码。
---

# explore

**硬边界：绝不修改正式业务代码。** 原型仅允许写在隔离临时目录，且必须明确标明用完即扔。

立即读取并执行 [`../jstack/playbooks/explore.md`](../jstack/playbooks/explore.md)，同时遵守 [`../jstack/SKILL.md`](../jstack/SKILL.md) 中的六条铁律与 [`../jstack/references/host-dispatch.md`](../jstack/references/host-dispatch.md) 中的只读侦察 / 跨厂商评审派发方式。

执行要点：
- 先抓现场证据（日志、请求、真实输出、git 历史），每条结论明确标注 `实测`、`推断` 或 `未验证`。
- 跨厂商评审前，若仓库 base 分支存在 `REVIEW.md`，先从 base 分支读取作为基准。
- 探索结束只交带证据的结论、方案对比或四类评审清单，不越权直接改业务代码。
