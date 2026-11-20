# jayden-stack

面向 **Cursor** 与 **Codex** 双宿主的中文 Agent 工程工作流插件（基于 Lauren Tan 的 [pstack](https://github.com/cursor/plugins/tree/main/pstack) 与 [open-pstack](https://github.com/ericlitman/open-pstack) 深度翻译、去 Cursor 内部耦合，并融合 ADR 维护、双轴评审、收尾复盘棘轮与个人验证闭环）。

核心主张：**欲速则先深挖。写更少但更稳的代码；每一项结论靠真实运行证据说话，每一项任务一路收到约定的终态。**

---

## 快速安装

### 方式一：按项目目录安装（不装全局、团队 `git pull` 即用）

适合**只在单个仓库启用**，或**直接提交进项目仓库让团队成员拉代码后开箱生效**。Cursor 与 Codex 都原生自动扫描项目里的 `.agents/skills/`，只需存一份即可双端共用：

```bash
git clone --depth 1 https://github.com/jiahao-jayden/jayden-stack.git /tmp/jayden-stack
SRC=/tmp/jayden-stack/plugins/jstack
DST="$(pwd)"   # 在目标业务仓库根目录执行

mkdir -p "$DST/.agents/skills" "$DST/.agents/agents" "$DST/.agents/scripts" "$DST/.cursor/agents"
for s in jstack explore build ship handoff verify-setup setup; do
  rm -rf "$DST/.agents/skills/$s"
  cp -R "$SRC/skills/$s" "$DST/.agents/skills/$s"
done
for a in scout worker reviewer comment-sicko; do
  cp "$SRC/agents/$a.md" "$DST/.cursor/agents/$a.md"
done
ln -sf "../../.cursor/agents/comment-sicko.md" "$DST/.agents/agents/comment-sicko.md"
cp "$SRC/scripts/run-external.mjs" "$DST/.agents/scripts/run-external.mjs"
chmod +x "$DST/.agents/scripts/run-external.mjs" \
  "$DST/.agents/skills/jstack/scripts/worktree-audit.sh" \
  "$DST/.agents/skills/ship/scripts/log.sh"
rm -rf /tmp/jayden-stack
```

安装后在仓库内生成的目录：
- `.agents/skills/{jstack,explore,build,ship,handoff,verify-setup,setup}/`：7 个顶层 Skill 与底层全部 Playbook / References（Cursor 与 Codex 共用）；
- `.cursor/agents/{scout,worker,reviewer,comment-sicko}.md`：Cursor 原生子代理；
- `.agents/agents/comment-sicko.md` 与 `.agents/scripts/run-external.mjs`：相对路径锚点与 Codex 跨厂商评审脚本。

### 方式二：原生插件管理器安装（个人全机复用）

#### Codex
```bash
# 安装
codex plugin marketplace add https://github.com/jiahao-jayden/jayden-stack
codex plugin add jstack@jayden-stack

# 更新 / 卸载
codex plugin marketplace upgrade
codex plugin remove jstack@jayden-stack && codex plugin marketplace remove jayden-stack
```
> 若想默认关闭、只对指定项目开启：在 `~/.codex/config.toml` 将 `[plugins."jstack@jayden-stack"]` 设为 `enabled = false`，再在目标项目根目录新建 `.codex/config.toml` 写入 `enabled = true`。

#### Cursor
```bash
# 添加 marketplace
agent plugin marketplace add https://github.com/jiahao-jayden/jayden-stack

# 更新 / 移除 marketplace
agent plugin marketplace update jayden-stack
agent plugin marketplace remove jayden-stack
```
添加完成后，在 Cursor 侧边栏 **Customize**（或 CLI 输入 `/plugins`）找到 **`jstack`** 点击 **Install**，按需选择 **Project（当前项目）** 或 **User（全局）** 作用域即可。

---

## 日常怎么用

日常直接按目标描述任务，前 5 个入口自动按场景路由；后 2 个工具入口默认不注入上下文，仅在手动敲命令时触发：

- **查原理 / 追历史 / 审 PR / 做抛弃式原型** → `/explore`（硬边界：不动正式业务代码）
- **修 Bug / 做功能 / 重构 / 性能优化 / 像素对齐** → `/build`
- **清注释 / 开 PR / 盯 CI / 合并 / 长任务 / 复盘棘轮** → `/ship`
- **暂停交接 / Cursor ↔ Codex 接手 / 清旧 worktree** → `/handoff`
- **跨阶段或拿不准入口** → `/jstack`（自动路由全部 22 个 Playbook）
- **为当前仓库生成或校准项目专属 `verify-<app>` 验证闭环（仅手动）** → `/verify-setup`
- **体检并初始化本机 Cursor & Codex 子代理与 `claude` 跨厂商评审通道（仅手动）** → `/setup`

---

## 结构设计：顶层极简，底层厚实

对外只暴露 **7 个顶层 Skill**（不污染斜杠命令面板和上下文 catalog），把 **22 个完整 Playbook** 与 **19 份专项参考手册**按阶段收纳在 `playbooks/` 与 `references/` 下按需读取、零预注入：

| 顶层 Skill | 调用方式 | 定位与内部收纳 |
| --- | --- | --- |
| **`/jstack`** | 自动 / 手动 | 总入口：22 个中文 Playbook + `worker.md` 穿透规则 + `references/{principles,adr,host-dispatch,bugbot-triage}.md` + `scripts/worktree-audit.sh` |
| **`/explore`** | 自动 / 手动 | 探索、诊断、原型与只读评审：`references/{how,why,code-review,interrogate,blast-radius}.md`；路由 `investigation` / `runtime-forensics` / `trace-forensics` / `prototype` |
| **`/build`** | 自动 / 手动 | 正式开发、重构与性能自证：`references/{architect,arena,swarm,figure-it-out,tdd,benchmark-checklist}.md`；路由 `bug-fix` / `feature` / `refactoring` / `perf-issue` / `hillclimb` / `visual-parity` / `multi-phase-plan` |
| **`/ship`** | 自动 / 手动 | 收口交付到约定终态：`references/{no-comments,correct,retro,show-me-your-work,decision-log-template.tsv}` + `scripts/log.sh`；路由 `opening-a-pr` / `babysit` / `shipping` / `autonomous-run` / `autopilot-full` / `autopilot-stack` |
| **`/handoff`** | 自动 / 手动 | 跨会话 / Cursor ↔ Codex 交接与清理：路由 `pause-safely` / `session-pickup` / `worktree-cleanup` |
| **`/verify-setup`** | **仅手动** | 为任意仓库首次生成或定期校准项目专属 `verify-<app>` skill 与 Feature Map |
| **`/setup`** | **仅手动** | 一键体检并初始化本机 Cursor 与 Codex 双宿主子代理及跨厂商评审通道 |

> `setup` 与 `verify-setup` 已通过 Cursor frontmatter（`disable-model-invocation: true`）与 Codex `agents/openai.yaml`（`allow_implicit_invocation: false`）双宿主设为默认不注入。

---

## 内置核心机制

1. **六条工程铁律与三轮熔断**：一路收到约定终态并读回核验；UI 改动真实环境正反态全测；结论带现场证据并标注 `实测 / 推断 / 未验证`；多入口开工列清单逐项核对；严守指定设备与运行方式（热更新/真机/禁模拟器）；同一前提连挂 2 次先质疑前提，累计 3 轮修不过直接熔断带证据找人。
2. **鲜活子代理与跨厂商第二意见**：修补轮与下一条任务默认开干净子代理（首行强制读 `worker.md` 穿透规则）；高风险评审、赛马裁判与长任务审计调用跨厂商强模型（Cursor 用原生 `Task` 的 `model`，Codex 用 `scripts/run-external.mjs` 调本机 `claude` CLI，失败显式报 `dropout`，绝不静默降级）。
3. **反水文 ADR 维护（`jstack/references/adr.md`）**：代码库是“现状事实”的唯一真相，ADR 严禁复述现状和流水账，只记**约束未来改动的取舍（`accepted`）**与**尚未落成代码的未决分叉（`proposed` / `open`）**；全流程自然联动（`explore` 查栅栏 → `build` 改前核对与拍板起草 → `ship` 注释升格、状态收口与复盘沉淀）。
4. **分层评审体系**：常规 PR 走 `code-review`（Standards + Spec 双轴并行只读体检，从 base 分支读 `REVIEW.md` 防篡改）；高风险改动走 `interrogate`（对抗式破坏审查，无复现证明一律丢弃）与 `blast-radius`（5 级确定性证明）。
5. **收尾复盘与棘轮固化（`retro` + `correct`）**：收尾时同时扫**过程证据（transcript 里的踩坑、绕路、人工纠偏、被否决方案）**与**成品证据（diff / PR 评论 / CI 报错）**，按「架构消灭 > 类型/Lint > 行为测试 > 文档规则」优先级把摩擦锁死，并同步清理过时文档。

---

## 22 个 Playbook 全览（`plugins/jstack/skills/jstack/playbooks/`）

- **调查、修复与性能（6）**：`investigation`、`bug-fix`、`perf-issue`、`hillclimb`、`runtime-forensics`、`trace-forensics`
- **开发、设计与评估（6）**：`feature`、`refactoring`、`prototype`、`visual-parity`、`authoring-a-skill`、`eval`
- **PR、发布与长任务（6）**：`opening-a-pr`、`babysit`、`shipping`、`autonomous-run`、`autopilot-full`、`autopilot-stack`
- **跨阶段、交接与维护（4）**：`session-pickup`、`pause-safely`、`multi-phase-plan`、`worktree-cleanup`

---

## 双宿主细节

- **Cursor**：通过 `.cursor-plugin/marketplace.json` 或项目级 `.agents/skills/` + `.cursor/agents/` 加载全部 7 个顶层 Skills 与 4 个原生 Agents（`scout` / `worker` / `reviewer` / `comment-sicko`）。
- **Codex**：通过 `.agents/plugins/marketplace.json` 或项目级 `.agents/skills/` 加载同一套 7 个顶层 Skills；可运行 `/setup` 生成 `~/.codex/agents/{scout,worker,reviewer}.toml` 并验证本机 `claude` 跨厂商只读评审通道。

## 开源协议

MIT License。致谢 Lauren Tan 的 `pstack` 与 `open-pstack` 社区，详见 [LICENSE](./LICENSE)。
