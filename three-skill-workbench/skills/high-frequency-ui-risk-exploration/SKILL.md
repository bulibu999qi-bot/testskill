---
name: high-frequency-ui-risk-exploration
description: Apply reusable high-frequency UI risk probes during Web exploration. Use with mode=explore profile=high-frequency or the matching workflow profile. Do not use for static PRD review alone.
---

# 高频 UI 风险探索

把本 Skill 作为 `reverse-test-workbench` 的策略层使用。浏览器执行、风险分级、证据、批次调度、断点和报告仍完全遵守 `reverse-test-workbench`；本 Skill 只决定哪些高频风险值得探测、何时适用以及结论能说到什么程度。

## 启动条件

- `mode=explore profile=high-frequency`
- `mode=workflow profile=high-frequency` 的探索阶段
- 用户显式指定 `skill=high-frequency-ui-risk-exploration`

直接指定本 Skill 时，仍须同时加载并执行 `reverse-test-workbench`。没有精确 Web URL 或 Playwright 执行器不可用时，按其规则阻塞，不用静态推演冒充执行。

## 覆盖目标

优先覆盖以下五类风险：

1. 交互与跳转：按钮、链接、页签、弹窗、返回、取消、重复操作及目标页上下文。
2. 表单与校验：必填、长度、格式、前后端校验、错误恢复和数据副作用。
3. 展示结构：字段、层级、空态、遮挡、截断、列表与详情可理解性。
4. 流程与状态：可见状态、合法操作、重复操作、刷新回显和状态迁移自洽性。
5. 数据一致性：输入与回显、列表与详情、操作前后、明细与汇总、刷新后持久化。

稳定性与恢复只在页面出现加载、异步、超时、刷新或可恢复异常线索时加入。二维码、真实移动设备、跨角色闭环和逐字文案核对默认不属于核心承诺，除非用户另行要求且能力具备。

## 执行流程

1. 完成 `reverse-test-workbench` 的执行器门禁、入口判定和 B01 快速地图。
2. 若存在 `requirement-exploration-handoff.json`，读取其中的确认事实、风险和未知项；它只提供探索方向，不替代页面证据。
3. 在任何高频 profile 探针执行前，询问一次本轮数据权限：

   `本轮是否允许 AI 创建并保存测试数据？请选择：A. 仅浏览和填写，不保存；B. 允许创建并保存 AI 自建数据。提交、审批、驳回、撤回等流程动作仍会另行确认。`

4. 用户未回答或回答不明确时按 A 执行。填写后不保存、不提交，并恢复页面；不能可靠恢复时跳过填写。
5. 基于页面实际结构读取 `references/applicability-rules.md`，只选择适用探针；不要把探针目录机械套到每个页面。
6. 使用 `references/probe-catalog.md` 组织一个或多个业务批次。每个探针记录目标风险、适用证据、动作等级、观察点、结果和未覆盖原因。
7. 使用 `references/assertion-boundaries.md` 区分确认缺陷、疑似缺陷、风险、当前行为基线和待确认规则。
8. 按 `reverse-test-workbench` 的现有集合写入 `run-data.json`，不新增顶层字段或报告体系。

## 写入与流程动作边界

- A 模式：允许 L0 浏览和在可恢复表单中临时填写；禁止保存、提交、上传、创建记录或触发状态变化。
- B 模式：允许创建、保存草稿和编辑 AI 自建数据；在 `test_data` 中记录可追踪标识和当前状态。
- 提交、审批、驳回、撤回、作废、删除、导入导出或其他 L2 动作，不被 B 模式自动授权。每次按对象、影响和可回滚性单独确认。
- 真实业务数据、权限/配置变化、外部通知、支付结算和不可恢复操作保持 L3，必须暂停。
- 单角色运行只对当前账号可见与可操作范围作结论。需要其他角色才能验证的流程、按钮、数据范围和状态回显写为未覆盖或阻塞。

## 799 字符探针

仅对适用的自由文本字段使用。执行前读取 `references/applicability-rules.md` 的排除条件。

- 已知最大长度小于 799 时，不输入 799；改测 `max-1`、`max`、`max+1` 中安全且适用的边界。
- 最大长度未知或不小于 799 时，生成恰好 799 个 ASCII 字符的可追踪值。
- 使用前缀 `HF799-{run_id}-{field_id}-`，将字段标识规范为 ASCII 字母、数字和连字符；用 `0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ` 循环补齐，再截断到 799。
- 通过浏览器上下文计算并断言 `value.length === 799`，不要依赖人工计数或模型目测。
- A 模式只填写并观察客户端行为，随后清空或取消；B 模式也只有在当前保存动作已获授权时才能持久化。
- 需要保存/提交才能观察的服务端校验，在没有相应授权时记录为未覆盖，不把“客户端允许输入”写成“系统支持 799 字符”。

## 需求交接消费

交接文件只接受以下证据等级：

- `confirmed`：可作为探索优先级和弱断言来源，仍需页面验证。
- `inferred`：只作为假设。
- `unknown`：转为观察问题或未覆盖项，不自行补规则。

页面事实与交接冲突时，以页面证据记录当前行为，同时保留冲突和需求来源，不覆盖原始口径。

## 运行数据映射

- `batches`：记录高频 profile 批次与适用探针集合。
- `paths`：记录交互、流程和跨页面一致性链路。
- `issues`：记录有可复现证据的问题，`issue_source` 使用 `high-frequency-risk-profile`。
- `risks`：记录待确认、单角色限制、未授权写入和无法执行的服务端校验。
- `test_data`：记录 AI 自建数据、799 字符值的标识、长度、字段和清理状态，不保存敏感信息。
- `knowledge`：记录事实、基线、确认规则和已失效假设，`source` 使用 `high-frequency-risk-profile` 或交接文件来源。
- `evidence`：沿用现有证据索引；不为了 profile 重复截图。

## 资源加载

- 选择风险方向时读取 `references/risk-taxonomy.md`。
- 设计具体动作时读取 `references/probe-catalog.md`。
- 判断页面、字段和探针是否适用时读取 `references/applicability-rules.md`。
- 定性问题和控制结论强度时读取 `references/assertion-boundaries.md`。

## 收口检查

- 已说明数据权限选择、单角色边界和流程动作授权状态。
- 每个已执行探针均有页面适用依据和可观察结果。
- 799 字符值已程序化校验长度，且未误用于受排除字段。
- 未保存的输入已清理或明确记录无法恢复。
- 未授权的保存、提交和状态动作保持未执行。
- 报告沿用 `reverse-test-workbench` 默认产物，不新增平行事实源。
