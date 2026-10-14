# 高频探索结构化交接

仅在 `mode=requirement profile=high-frequency` 或 `mode=workflow profile=high-frequency` 时生成 `requirement-exploration-handoff.json`。该文件服务于后续 UI 探索，不替代需求评审、测试设计或正式用例。

## 生成原则

- 只转换需求阶段已有事实、合理推导和未知项，不执行浏览器探针。
- 不改变正式用例表字段、数量、Excel 或 HTML 合同。
- `confirmed` 仅用于需求原文、用户确认或可靠接口/验收规则。
- `inferred` 仅用于明确标注的合理推导。
- 信息不足使用 `unknown` 和 `null`，不得补造字段限制、流程顺序、角色或预期。
- L0 未通过 P0 门禁时允许生成 `status=blocked` 的交接，但不得生成正式用例；其内容只包含已知事实和待确认项。

## 固定结构

顶层键及顺序固定如下：

```json
{
  "schema_version": "1.0",
  "profile": "high-frequency",
  "status": "ready",
  "source": {
    "input_level": "L2",
    "source_name": "",
    "generated_at": "",
    "scope_summary": ""
  },
  "modules": [],
  "cross_module_relations": [],
  "priority_risks": [],
  "unknowns": [],
  "execution_constraints": {
    "role_mode": "single_role",
    "available_role": null,
    "test_data_creation": "ask_before_exploration",
    "save_permission": "unknown",
    "workflow_actions": "confirm_each_action",
    "restricted_actions": []
  }
}
```

`status` 只使用：

- `ready`：可供探索消费。
- `partial`：有有效方向，但存在会限制断言或路径的未知。
- `blocked`：L0 P0 核心规则未确认，或没有可定位模块/页面/对象的信息。

## 模块对象

`modules` 中每项固定使用：

```json
{
  "module_id": "M01",
  "module_name": "",
  "page_or_entry": "",
  "role_context": [],
  "fields": [],
  "confirmed_copy": [],
  "states": [],
  "transitions": [],
  "data_relations": [],
  "risk_focus": [],
  "unknown_refs": []
}
```

字段对象：

```json
{
  "field_id": "M01-F01",
  "name": "",
  "type": "",
  "required": null,
  "min_length": null,
  "max_length": null,
  "format": "",
  "evidence_level": "unknown",
  "source_ref": ""
}
```

状态迁移对象：

```json
{
  "from": "",
  "action": "",
  "to": "",
  "role": "",
  "evidence_level": "unknown",
  "source_ref": ""
}
```

数据关系对象：

```json
{
  "relation_id": "M01-R01",
  "left": "",
  "right": "",
  "expected_relation": "",
  "evidence_level": "unknown",
  "source_ref": ""
}
```

`confirmed_copy` 每项包含 `location`、`text`、`source_ref`。只有逐字内容已确认时才写入；仅有含义时留在普通规则中。

## 跨模块关系与风险

`cross_module_relations` 每项使用：

```json
{
  "relation_id": "XR01",
  "source_module": "",
  "target_module": "",
  "expected_relation": "",
  "evidence_level": "unknown",
  "source_ref": ""
}
```

`priority_risks` 每项使用：

```json
{
  "risk_id": "R01",
  "priority": "P0",
  "category": "data_consistency",
  "description": "",
  "module_refs": [],
  "evidence_level": "confirmed",
  "source_ref": "PRD 3.2"
}
```

`priority` 只使用 `P0`、`P1`、`P2`。`category` 优先使用：

```text
interaction_navigation
form_validation
display_structure
workflow_state
data_consistency
stability_recovery
```

## 未知项

`unknowns` 每项使用：

```json
{
  "unknown_id": "U01",
  "priority": "P0",
  "question": "",
  "impact": "",
  "module_refs": []
}
```

模块中的 `unknown_refs` 只引用顶层 `unknown_id`，不重复写问题正文。

## 执行约束

- `role_mode` 默认 `single_role`；只有输入明确提供多角色和账号能力时才能改写。
- `available_role` 仅在用户或需求明确时填写，否则为 `null`。
- `test_data_creation` 固定为 `ask_before_exploration`，除非用户当前消息已明确授权或禁止。
- `save_permission` 只使用 `unknown`、`allowed_ai_created_data`、`read_only`。
- `workflow_actions` 固定为 `confirm_each_action`。
- `restricted_actions` 记录需求明确禁止或高风险的动作，不推断用户授权。

## 文件校验

生成后必须重新读取并确认：

1. JSON 可解析且顶层键完整。
2. `profile` 为 `high-frequency`，`schema_version` 为 `1.0`。
3. ID 在各集合内唯一，所有引用存在。
4. 每项规则都有 `evidence_level` 与 `source_ref`；无来源的项必须是 `unknown`。
5. 文件不含账号密码、Cookie、token、真实个人信息或需求未要求对外保存的内部标识。
