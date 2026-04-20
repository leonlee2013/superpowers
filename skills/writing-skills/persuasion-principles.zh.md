# Skill 设计中的说服原则

## 概述

LLM 会像人类一样受到相同说服原则的影响。理解这种心理学有助于你设计更有效的 skill——不是为了操控，而是为了确保关键实践即使在压力下也能被遵循。

**研究基础：** Meincke 等人 (2025) 使用 N=28,000 次 AI 对话测试了 7 种说服原则。说服技巧使合规率提升了一倍以上（33% -> 72%，p < .001）。

## 七大原则

### 1. 权威性
**定义：** 对专业知识、资质或官方来源的服从。

**在 skill 中的运作方式：**
- 命令式语言："YOU MUST"、"Never"、"Always"
- 不可商量的框架："No exceptions"
- 消除决策疲劳和合理化借口

**何时使用：**
- 强制纪律的 skill（TDD、验证要求）
- 安全关键实践
- 已确立的最佳实践

**示例：**
```markdown
✅ Write code before test? Delete it. Start over. No exceptions.
❌ Consider writing tests first when feasible.
```

### 2. 承诺一致性
**定义：** 与先前的行为、声明或公开承诺保持一致。

**在 skill 中的运作方式：**
- 要求宣告："Announce skill usage"
- 强制明确选择："Choose A, B, or C"
- 使用跟踪：TodoWrite 用于清单

**何时使用：**
- 确保 skill 确实被遵循
- 多步骤流程
- 问责机制

**示例：**
```markdown
✅ When you find a skill, you MUST announce: "I'm using [Skill Name]"
❌ Consider letting your partner know which skill you're using.
```

### 3. 稀缺性
**定义：** 由时间限制或有限可用性产生的紧迫感。

**在 skill 中的运作方式：**
- 有时限的要求："Before proceeding"
- 顺序依赖关系："Immediately after X"
- 防止拖延

**何时使用：**
- 即时验证要求
- 时间敏感的工作流
- 防止"以后再做"

**示例：**
```markdown
✅ After completing a task, IMMEDIATELY request code review before proceeding.
❌ You can review code when convenient.
```

### 4. 社会认同
**定义：** 遵从他人的行为或被认为是常规的做法。

**在 skill 中的运作方式：**
- 普遍模式："Every time"、"Always"
- 失败模式："X without Y = failure"
- 建立规范

**何时使用：**
- 记录普遍实践
- 警告常见失败
- 强化标准

**示例：**
```markdown
✅ Checklists without TodoWrite tracking = steps get skipped. Every time.
❌ Some people find TodoWrite helpful for checklists.
```

### 5. 统一性
**定义：** 共同身份感、"我们"的归属感、群体内认同。

**在 skill 中的运作方式：**
- 协作性语言："our codebase"、"we're colleagues"
- 共同目标："we both want quality"

**何时使用：**
- 协作工作流
- 建立团队文化
- 非等级制实践

**示例：**
```markdown
✅ We're colleagues working together. I need your honest technical judgment.
❌ You should probably tell me if I'm wrong.
```

### 6. 互惠性
**定义：** 回报所受恩惠的义务感。

**运作方式：**
- 谨慎使用——可能显得操控性强
- 在 skill 中很少需要

**何时避免：**
- 几乎总是（其他原则更有效）

### 7. 好感
**定义：** 偏好与自己喜欢的人合作。

**运作方式：**
- **不要用于合规目的**
- 与诚实反馈文化冲突
- 会导致谄媚

**何时避免：**
- 在纪律执行中始终避免

## 各类 Skill 的原则组合

| Skill 类型 | 使用 | 避免 |
|------------|-----|-------|
| 强制纪律型 | 权威性 + 承诺一致性 + 社会认同 | 好感、互惠性 |
| 指导/技术型 | 适度权威性 + 统一性 | 过度权威 |
| 协作型 | 统一性 + 承诺一致性 | 权威性、好感 |
| 参考型 | 仅需清晰度 | 所有说服手段 |

## 为什么有效：心理学原理

**明确界限规则减少合理化：**
- "YOU MUST" 消除决策疲劳
- 绝对性语言排除了"这算不算例外？"的问题
- 明确的反合理化措施封堵具体漏洞

**执行意图创造自动行为：**
- 清晰的触发条件 + 必需的行动 = 自动执行
- "When X, do Y" 比 "generally do Y" 更有效
- 降低合规的认知负荷

**LLM 具有类人特征：**
- 基于包含这些模式的人类文本训练
- 训练数据中权威性语言先于合规行为出现
- 承诺序列（声明 -> 行动）被频繁建模
- 社会认同模式（everyone does X）建立规范

## 伦理使用

**合理的：**
- 确保关键实践得到遵循
- 创建有效的文档
- 防止可预见的失败

**不合理的：**
- 为个人利益而操控
- 制造虚假紧迫感
- 基于愧疚的合规

**判断标准：** 如果用户完全理解这种技巧，它是否仍然符合用户的真正利益？

## 研究引用

**Cialdini, R. B. (2021).** *Influence: The Psychology of Persuasion (New and Expanded).* Harper Business.
- 七大说服原则
- 影响力研究的实证基础

**Meincke, L., Shapiro, D., Duckworth, A. L., Mollick, E., Mollick, L., & Cialdini, R. (2025).** Call Me A Jerk: Persuading AI to Comply with Objectionable Requests. University of Pennsylvania.
- 使用 N=28,000 次 LLM 对话测试了 7 种原则
- 说服技巧使合规率从 33% 提升至 72%
- 权威性、承诺一致性、稀缺性最为有效
- 验证了 LLM 行为的类人模型

## 快速参考

设计 skill 时，问自己：

1. **这是什么类型？**（纪律型 vs. 指导型 vs. 参考型）
2. **我试图改变什么行为？**
3. **适用哪些原则？**（纪律型通常用权威性 + 承诺一致性）
4. **是否组合了太多原则？**（不要七种全用）
5. **这合乎伦理吗？**（是否符合用户的真正利益？）
