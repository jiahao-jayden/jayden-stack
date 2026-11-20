---
name: why
description: 从 git 提交历史、PR/Issue 讨论与决策记录（而非只靠当前源码）追溯“为什么这样设计”、回归来历与阈值依据。问当前运行机制请用 /how。
---

# Why（查历史记录追溯设计动机）

**从历史记录找答案，而不是对着当前源码看图说话。**

1. 用 `git log -S` / `git log -L` / `git blame` 找出引入目标代码的原始 commit 以及后续每一次关键演进 commit。
2. 必要时结合 `gh pr list --search` / `gh pr view`、Issue 讨论与仓库决策文档（如 `docs/decisions.md`），还原当时面临的约束、排除过的替代方案和真实线上事故背景。
3. **严格区分记录与推断**：
   - 直接引用原作者在 commit message、PR 描述或决策文档里写下的理由（附 commit SHA / PR 号）；
   - 你基于上下文补全的推测必须显式标注为 `推断`；
   - 历史记录确实没有交代的部分，直白写出“现有记录未说明这一点”，绝不编造合理化故事。
