# Worktree cleanup（清理旧工作区与缓存）

**这是唯一一个会直接删除本地状态、且没有代码评审兜底的剧本——下面每一步门禁就是你的代码评审，一步不能省。**

1. **先抓磁盘快照并运行审计脚本（造杠杆工具）**：
   - 记录清理前的 `df -h /`。
   - 运行插件自带脚本：`bash <jstack目录>/scripts/worktree-audit.sh [-b <pr-base-ref>] [repo-path]`。
   - 不传 `-b` 时默认按 `origin/HEAD` 判断是否 `MERGED`；若目标仓库 PR 指向其他主干分支，显式传 `-b origin/<branch>`。
   - **绝不手打猜测 worktree 路径**——脚本严格从 `git worktree list` 读取，避免漏掉 `.cursor/worktrees/<repo>/...` 等隐藏路径。脚本会按体积、年龄、合并态、未提交改动、PR 状态和最近会话给出分类建议（扫描 transcript 较慢时放后台运行）。
2. **脚本分类只是建议，绝不是删除许可**：
   - 真正活跃或置顶的会话才是唯一真相——向用户或工作区核对当前仍在使用/置顶的会话集合，交叉核验每个候选项。置顶集合优先级永远高于脚本标记的 `safe`。
3. **删除前核查疑似活跃条目**：
   - 对每一条 `verify-recent-chat` 或拿不准的 worktree，派只读 `scout` 扫描对应 transcript，核实该会话是否仍在进行、以及它是否通过后台子代理（如 `arena` 或复现实验）占用了兄弟 worktree。
4. **遇到不可逆损失必须停下确认**：
   - `wip:N`（存在已追踪的未提交修改）：展示 `git diff --stat` 并让用户拍板——删干净 worktree 还能从分支找回，删未提交改动就彻底没了；
   - `scratch:N`（未追踪临时文件）：列出文件名再删；
   - 按自主权规则：确认「干净 + 已合并 + 无人使用」的可直接清理；凡是「有 `wip` 或仍在使用」的一律暂停保留。
5. **安全清理已确认的集合**：
   - 逐路径执行 `git worktree remove --force <path>`；若因被忽略的构建产物导致目录残留，确认路径无误后 `rm -rf <path>`，最后跑 `git worktree prune`（分支引用保留，不丢 commit）。
   - 重跑 `df -h /` 与 `git worktree list` 确认释放效果。
6. **其他大头磁盘回收（仅在用户要求清理缓存/模拟器时执行）**：
   - **注意个人红线**：若项目禁止安装/使用 iOS 模拟器，可按用户授权清理遗留的无用模拟器镜像与 Xcode `DerivedData` / `iOS DeviceSupport`（如 `xcrun simctl delete unavailable`、`xcrun simctl --set testing delete all`），以及用户明确允许清理的包缓存（pnpm / uv / brew / yarn）或旧工作区快照。绝不擅自删除用户声明要保留的缓存。

**回复要求**：清理前后的 `df -h /` 与实际回收空间、已删除的 worktree 清单，以及每个被保留条目的单行理由（哪个会话在用、或存在未提交改动）。
