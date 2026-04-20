# 代码质量审查 Prompt 模板

在派发代码质量审查 subagent 时使用此模板。

**用途：** 验证实现是否构建良好（整洁、经过测试、可维护）

**仅在规格合规审查通过后才派发。**

```
Task tool (superpowers:code-reviewer):
  Use template at requesting-code-review/code-reviewer.md

  WHAT_WAS_IMPLEMENTED: [from implementer's report]
  PLAN_OR_REQUIREMENTS: Task N from [plan-file]
  BASE_SHA: [commit before task]
  HEAD_SHA: [current commit]
  DESCRIPTION: [task summary]
```

**除标准代码质量关注点外，审查者还应检查：**
- 每个文件是否有一个明确的职责和定义清晰的接口？
- 各单元是否已分解到可以独立理解和测试的程度？
- 实现是否遵循了计划中的文件结构？
- 此次实现是否创建了已经很大的新文件，或显著增大了现有文件？（不要标记已有的文件大小——关注此次变更贡献了什么。）

**代码审查者返回内容：** 优点、问题（严重/重要/轻微）、评估
