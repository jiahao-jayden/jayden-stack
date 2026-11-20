# Show me your work（可审计 TSV 决策轨迹日志）

长任务或无人值守运行时，只维护一份权威决策轨迹日志，让审阅者离开几小时回来能顺着证据链直接信任结果。

## 一、日志格式

单个 TSV 文件，每次关键决策一行；所有单元格严格单行，**证据列只放指针，不写小作文**。

表头模板位于 [`decision-log-template.tsv`](decision-log-template.tsv)：
- `ts`：ISO8601 时间戳；
- `phase`：所属阶段或工作流；
- `decision`：一句话说清选了什么或做了什么；
- `why`：大白话说清原因（不要堆术语标签）；
- `evidence`：可查证的指针（commit SHA、PR 号、`file:line`、trace/截图路径）；
- `result`：实测结果或谓词状态（如 `tests green`、`reverted`、`pixel-diff 0`、`INCONCLUSIVE`、`open`）。

## 二、如何安全追加一行

优先调用自带脚本（自动打时间戳、首次自动写表头、清洗换行/Tab 并防表格公式注入）：
```bash
bash <ship目录>/scripts/log.sh <logfile> <phase> <decision> <why> <evidence> <result>
```

只记录**真正影响走向的节点**：方案分叉、切片完成及验证结果、方向转向/回退及诱因、阻塞上报、门禁修改（循环任务每轮一行）。琐碎动作不要刷屏。

**多轮/跨会话标记**：同一个会话连续追加直接写；当一个新会话或替换代理续写已有日志时，先读末尾几行，第一行用 `phase=start` 标明本轮接手（`evidence` 填本代理标识），便于末尾分段审计。

## 三、存放位置与只增不删纪律

- 默认放在工作区外或 gitignore 位置（如 `decisions.tsv` 或 `.audit/<task-slug>.tsv`），不随手提交；只有大工程需要随 PR 供审阅者核查时才一并提交。
- **严格只追加（Append-only）**：之前的判断错了，追加一条新行覆盖更正，**绝不回头篡改或删除历史行**。

## 四、交卷前对照 transcript 自审

交付前，读取本次会话在当前工作区 `agent-transcripts/` 下的记录（绝不跨项目扫描其他工作区），逐行核对本轮写下的轨迹：
- 每一行是否都对应真实发生过的动作与决策？
- 每一个证据指针是否真实存在且真的证明了该行声明？
- 是否有改变了走向的分叉、转向或废弃方案漏记了？漏记就追加补录，错记就追加更正行（绝不删原行）。

## 五、跨厂商轨迹审计（Cross-model review of the trail）

交付前，按 `../../jstack/references/host-dispatch.md` 派一名**与干活模型不同厂商**的只读 `reviewer`（Cursor 用跨厂商 `Task`，Codex 用 `scripts/run-external.mjs` 调本机 `claude`）通读决策轨迹与关键运行记录，专门揪出用户粗扫容易漏掉的隐患：
- 证据薄弱或缺失的决策行；
- 声称已验证但在记录里找不到实跑证据的步骤；
- 事后看过于草率、范围蔓延或只糊症状的选择。

凡是产出过轨迹日志的最终汇报，末尾必须附一个 **`需重点关注（Attention）`** 小节：第一行写明评审模型（或如实标注跨厂商评审 `dropout` 及原因），随后逐条列出风险行；确实无隐患时可写「无风险标记」。
