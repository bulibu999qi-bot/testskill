# Three Skill Workbench

一个统一测试工作台插件，通过 `mode` 路由需求分析、Web UI 逆向探索和 UI 验收，并通过可选 `profile` 为探索阶段增加专项策略。

## 模式

| 调用 | 执行 Skill | 主要用途 | 主要产物 |
| --- | --- | --- | --- |
| `mode=requirement` | `requirement-test-workbench` | PRD 评审、测试设计、测试用例 | 评审结论、测试点、用例及按需生成的 Excel/HTML |
| `mode=explore` | `reverse-test-workbench` | 缺少可靠需求时探索 Web 页面 | `过程小结.docx`、`测试资产表.xlsx`、`evidence/` |
| `mode=acceptance` | `workbench-ui-acceptance-execution` | 执行已有 UI 测试用例 | 聊天结论、截图和离线 HTML 报告 |
| `mode=workflow` | 依次串联三个阶段 | 从需求分析到探索和验收 | 各阶段结果与结构化交接 |

`profile=high-frequency` 是附加策略，不是新的顶层模式。未指定 profile 时，原有模式行为保持不变。

## 高频风险 Profile

| 调用 | 行为 |
| --- | --- |
| `mode=requirement profile=high-frequency` | 分析需求，并额外生成 `requirement-exploration-handoff.json` |
| `mode=explore profile=high-frequency` | 以逆向探索为执行内核，加载高频 UI 风险探针 |
| `mode=workflow profile=high-frequency` | 需求阶段生成交接，探索阶段消费交接并执行高频探针，验收阶段保持原合同 |

高频 profile 重点覆盖：

- 交互与跳转
- 表单与校验
- 展示结构
- 流程与状态
- 数据一致性

稳定性与恢复仅在页面存在异步、加载、超时或恢复线索时加入。二维码、真实移动设备、跨角色闭环和精确文案核对默认不属于核心范围。

## 调用示例

```text
mode=requirement 请评审这份 PRD，并输出测试设计

mode=requirement profile=high-frequency
请分析这份 PRD，并生成供后续 UI 探索使用的结构化交接

mode=explore
请从 https://example.com 开始做 UI 逆向探索

mode=explore profile=high-frequency
请从 https://example.com 开始做高频风险探索

mode=acceptance
请执行这份测试用例并生成验收报告

mode=workflow profile=high-frequency
请根据这份需求和测试地址完成需求分析、高频风险探索和 UI 验收
```

也可以使用 `skill=requirement-test-workbench` 等名称显式指定 Skill。直接指定 `skill=high-frequency-ui-risk-exploration` 时，仍会同时使用 `reverse-test-workbench` 的浏览器执行、风险、证据和报告规则。

## 需求交接

`mode=requirement profile=high-frequency` 不会执行浏览器操作，也不会修改正式测试用例表结构。它会额外生成 `requirement-exploration-handoff.json`，供后续探索阶段消费，内容包括：

- 模块、页面和入口
- 当前角色上下文
- 字段类型及已知必填、长度、格式规则
- 已确认文案
- 状态与迁移
- 列表、详情、统计等数据关系
- P0/P1 风险和待确认项
- 数据创建、保存和流程动作约束

每项规则区分 `confirmed`、`inferred` 和 `unknown`。需求未明确的内容不会被补造成确定规则。

## 数据与操作权限

高频探索在 B01 快速地图完成后、执行首个 profile 探针前，会询问本轮是否允许创建并保存 AI 自建数据：

- 仅浏览和填写：允许只读操作和可恢复的临时填写，不保存、不提交。
- 允许保存 AI 自建数据：允许创建、保存草稿和编辑本轮 AI 自建数据。

保存授权不包含提交、审批、驳回、撤回、作废、删除、导入导出等流程动作。这些动作仍需根据对象、影响范围和可恢复性单独确认。只有一个角色时，跨角色权限和流程结果会标记为未覆盖或阻塞。

## 799 字符探针

799 字符探针只适用于备注、说明、描述、意见、原因、标题、名称等自由文本字段：

- 密码、验证码、手机号、邮箱、URL、日期、数字、金额、搜索框等字段不会使用该探针。
- 已知最大长度小于 799 时，改测 `max-1`、`max`、`max+1`。
- 最大长度未知或不小于 799 时，生成带 `HF799-` 前缀且长度经过程序校验的确定性文本。
- 未获得保存授权时只填写并观察客户端行为，随后清空或取消。
- 只有保存并刷新后完整回显，才能说明该路径支持持久化 799 字符。

## Workflow 边界

`workflow` 不会虚构阶段输入或测试结果：

1. 需求输入不足时停在澄清门禁，未确认 P0 规则不会包装成正式用例。
2. 没有精确 URL 或 Playwright 执行器不可用时，探索阶段记录阻塞。
3. 验收缺少用例、账号、权限或前置数据时，验收阶段停止。
4. 后续阶段只消费前一阶段实际生成的资产；缺失内容保持待确认或未覆盖。
