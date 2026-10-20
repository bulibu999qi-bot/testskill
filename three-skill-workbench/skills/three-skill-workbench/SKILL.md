---
name: three-skill-workbench
description: "统一入口：按 mode 选择需求测试分析、Web UI 逆向探索或 UI 验收执行；也可按工作流串联三个 skill，并按需加载专项 profile。"
---

# Three Skill Workbench 统一入口

本入口负责路由，不替代下面专业 skill 的规则。执行前先判断模式和可选 profile，并完整遵守被路由 skill 的硬规则。

## 模式

优先识别用户显式参数（`mode=`、`skill=`）；没有参数时根据目标意图选择最小充分模式。

| 模式 | 路由 skill | 适用输入 | 主要产出 |
| --- | --- | --- | --- |
| `requirement` | `requirement-test-workbench` | PRD、用户故事、口头需求、接口需求或需求变更 | 需求评审、测试设计或测试用例 |
| `explore` | `reverse-test-workbench` | Web URL/页面，缺少可靠需求或接口文档 | 有证据的 UI 逆向探索、风险和可续跑资产 |
| `acceptance` | `workbench-ui-acceptance-execution` | 已有测试用例、环境地址和执行前置 | 浏览器 UI 验收、截图和离线 HTML 报告 |
| `workflow` | 按下述顺序串联 | 希望从需求到验收形成闭环 | 每阶段结论和下一阶段输入 |

## 可选 Profile

`profile` 是附加专项策略，不是新的顶层模式。当前支持 `high-frequency` 和 `long-text`。一次只能指定一个 profile；两者不得静默叠加。未指定 `profile` 时保持各模式原行为。

### `profile=high-frequency`

| 调用 | 路由 |
| --- | --- |
| `mode=explore profile=high-frequency` | 以 `reverse-test-workbench` 为执行内核，同时加载 `high-frequency-ui-risk-exploration` |
| `mode=workflow profile=high-frequency` | `requirement` 输出探索交接，`explore` 加载高频策略，`acceptance` 保持原合同 |
| `mode=requirement profile=high-frequency` | 仍由 `requirement-test-workbench` 分析需求，并额外输出独立探索交接 |

显式 `skill=high-frequency-ui-risk-exploration` 时，必须同时加载 `reverse-test-workbench`，不能脱离浏览器执行与证据规则单独运行。

### `profile=long-text`

`long-text` 仅检查表单内所有合格文本字段的精确 799 字符边界。加载 `long-text-submission-check`，并按其 `references/workbench-integration.md` 与所选核心 skill 协作。

| 调用 | 路由 |
| --- | --- |
| `mode=requirement profile=long-text` | 由 `requirement-test-workbench` 分析需求，只额外输出候选字段、长度规则、约束和未知项；不得声称页面字段实际可见或可编辑 |
| `mode=explore profile=long-text` | 以 `reverse-test-workbench` 为执行内核，完成页面实际字段盘点和逐字段输入检查；提交范围包含在任务中时，必须先按冻结清单获得专项授权 |
| `mode=acceptance profile=long-text` | 以 `workbench-ui-acceptance-execution` 为执行内核，消费可执行用例或完整交接，逐字段隔离提交并核验持久化 |
| `mode=workflow profile=long-text` | `requirement` 产出候选交接，`explore` 实盘点并完成输入阶段，`acceptance` 获专项授权后完成逐字段提交与持久化核验 |

显式 `skill=long-text-submission-check` 时可独立运行，并使用该专项自己的报告模板。通过 profile 调用时，执行、证据和报告仍由当前核心 skill 负责，不新建平行报告体系。

自然语言映射：

- “评审需求 / 提测试点 / 写测试用例 / 接口专项” -> `requirement`
- “打开网址看看 / 没有需求文档，探索一下 / 逆向测试” -> `explore`
- “跑用例 / UI 验收 / 截图并生成报告” -> `acceptance`
- “完整走一遍 / 从需求到验收 / 三个 skill 串起来” -> `workflow`
- “799 字符 / 超长文本 / 字符长度边界 / maxlength / 数据过长” -> 在所选模式上增加 `profile=long-text`

## `workflow` 串联协议

按以下顺序执行，每阶段结束时先输出阶段结论、证据、未解决问题和结构化交接信息：

1. `requirement`：先做输入质量判断。L0 需求必须停在澄清门禁；未确认 P0 规则时不得包装成正式用例。`profile=high-frequency` 时另行输出 `requirement-exploration-handoff.json`。`profile=long-text` 时只输出候选字段、规则、约束和未知项，不把需求推测写成页面事实。两种 profile 都不改变正式用例表结构。
2. `explore`：仅在有精确 Web URL 且 Playwright 执行器可用时进入。复用已确认的需求口径；没有 URL 或执行器能力时记录阻塞，不猜测页面事实。`profile=high-frequency` 时在 B01 后由高频策略选择适用探针，并先完成本轮数据写入授权。`profile=long-text` 时建立实际字段清单，每次只向一个字段输入精确 799 字符；若本阶段包含提交，按专项字段清单授权执行。
3. `acceptance`：仅在存在可执行用例、测试环境和必要账号/权限/数据时进入。使用前两阶段的确认口径和探索证据；缺少必要前置时停止并标记阻塞。`profile=long-text` 时按不变的目标页面与冻结字段清单请求或核验提交授权，每个授权字段最多创建一条隔离记录，并重新打开记录核对持久化内容。

阶段之间不得把“计划完成”当成“系统验证通过”。后续阶段只消费前一阶段实际交付的资产；不存在或不完整的资产必须明确标记为待确认/未覆盖。

## 输出约定

- 开头说明：`mode`、输入质量、当前阶段和范围边界。
- 指定 `profile` 时同时说明 profile、数据权限状态和额外覆盖边界。
- `profile=long-text` 时区分需求候选字段、页面实际字段、输入已检查字段和持久化已检查字段。
- 单模式只交付该 skill 定义的产物，不机械扩展成全量。
- `workflow` 以阶段小节分隔，并在末尾给出已回答、未回答、阻塞和下一步。
- 不编造账号、页面行为、接口字段、预期结果或测试通过结论。
- 用户未指定 `mode` 且意图无法可靠判断时，询问其目标；不要同时运行多个模式。
