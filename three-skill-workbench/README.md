# Three Skill Workbench

一个 Codex plugin，使用统一入口路由三个已有 skill：

- `requirement`：需求评审、测试设计和测试用例
- `explore`：无可靠需求文档时的 Web UI 逆向探索
- `acceptance`：基于已有用例的浏览器 UI 验收
- `workflow`：按需求分析、UI 探索、UI 验收的顺序串联

## 使用

在 Codex 中直接指定模式：

```text
mode=requirement 请评审这份 PRD，并输出测试设计
mode=explore 请从 https://example.com 开始做 UI 逆向探索
mode=acceptance 请执行这份测试用例并生成验收报告
mode=workflow 从这份需求和测试地址开始完成完整闭环
```

也可以使用 `skill=requirement-test-workbench` 等显式指定 skill 名称。未指定模式时，入口根据用户目标选择最小充分模式；无法可靠判断时会先询问目标。

`workflow` 不会虚构阶段输入：需求输入不足、没有精确 URL、浏览器执行器不可用，或验收缺少账号/权限/前置数据时，会在对应阶段标记阻塞并停止后续阶段。
