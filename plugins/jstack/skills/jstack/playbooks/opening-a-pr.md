# Opening a PR（准备一个可审的 PR）

其他开发类 Playbook 的标准收口步骤。如果本次用户约定的终态是“仅本地提交”或“推送分支”，做到对应节点并读回核验即可；如果约定提 PR / 盯 CI / 合入，则完整执行并衔接后续剧本。

1. **保持工作区纯净**：优先在基于主干切出的干净分支或独立 git worktree 工作，绝不把无关改动混进本次提交。
2. **提交前清理冗余（Deslop + Diff Checklist）**：
   - 通读完整 `git diff`，清掉多余调试日志、死代码、未调用的 helper、过度防御的重复判空与复述代码的废话注释（必要时跑 [no-comments](../../ship/references/no-comments.md)）。
   - 若本次改动拍板了满足门槛的架构决策/未决方向，或推翻/收口了既有 ADR，按 [`../references/adr.md`](../references/adr.md) 同步落盘或把旧 ADR 标为 `superseded`（不达门槛就不写）。
   - **顺手整理受影响的 `docs/`**：若本次改动了架构、接口、流程或目录结构，grep `docs/` 中提到相关路径或主题的文档——已过时或与代码/Skill 重复的当场修或删；新增或调整文档必须按职责分目录（`docs/adr/`、`docs/ops/`、`docs/guides/`、`docs/contracts/`），禁止平铺在 `docs/` 根，也严禁留一次性过程稿（详见 [retro](../../ship/references/retro.md)）。
   - 按 diff 类型触发轻量复核：状态/并发看重入与回滚；接口/Schema 看调用方已迁完且旧接口已删；UI/动效看正反状态与遮挡；构建/配置看真实打包启动。
   - 跑通仓库要求的全部本地检查（lint、type-check、测试），以最终真实输出为准。
3. **拆成小而清晰的可落地提交**：按逻辑依赖整理提交序列，遵循仓库提交规范（如 Conventional Commits），每个提交单独看都成立。
4. **打开 Ready 状态的 PR（不默认 Draft）**：
   - 标题：`type(scope): 简明主题`（核心变动由具体符号承载时带上符号名）。
   - 正文：控制在 40 行以内、一分钟可读完的简报，按顺序只保留非空小节：
     - `## 背景与目标`
     - `## 改动内容`（跨模块或改了数据/控制流时，顺手附一张 ≤10 行的最小 ASCII/Mermaid 结构图；单模块小改动不画）
     - `## 影响范围`
     - `## 关键取舍`（只写评审人很可能会追问的被否决方案，或附新增/变更的 ADR 路径）
     - `## 爆炸半径`（首行标明 **双向门（低成本可回滚）** 或 **单向门（改 Schema/线协议/数据迁移/对外契约，不可逆）**）
     - `## 验证证据`
   - 冗长运行日志和大表格放到独立文件或 PR 评论，正文里给链接。
5. **创建 PR 方式**：
   - 若基于其他 PR 叠加后续改动，显式指定对应的 `--base` 分支；
   - GitHub 仓库使用 `gh pr create`（不加 `--draft`），或通过当前宿主原生 PR 工具且 `draft: false`；Cursor Origin 仓库使用 `origin pr create --status open`。
   - 创建后读回 PR URL 与状态确认。打开 PR 本身不会自动开启持续跟进——若用户约定的终态包含绿 CI 或合并，紧接着执行 [`babysit.md`](babysit.md) / [`shipping.md`](shipping.md)。
