# Why（查历史记录追溯设计动机）

**从历史记录找答案，而不是对着当前源码看图说话。**

1. 先扫仓库决策文档（`docs/adr/` 或 `docs/decisions.md`，若存在，参见 [`../../jstack/references/adr.md`](../../jstack/references/adr.md)），快速确认目标区域是否存在已定不可破约束、排除过的方案或未决方向，避免把刻意设计误判成历史遗留。
2. 再用 `git log -S` / `git log -L` / `git blame` 找出引入目标代码的原始 commit 以及后续每一次关键演进 commit。
3. 必要时结合 `gh pr list --search` / `gh pr view` 与 Issue 讨论，还原当时面临的约束、排除过的替代方案和真实线上事故背景。
4. **严格区分记录与推断**：
   - 直接引用原作者在 commit message、PR 描述或决策文档里写下的理由（附 commit SHA / PR 号）；
   - 你基于上下文补全的推测必须显式标注为 `推断`；
   - 历史记录确实没有交代的部分，直白写出“现有记录未说明这一点”，绝不编造合理化故事。
