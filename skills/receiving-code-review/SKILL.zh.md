---
name: receiving-code-review
description: 在收到 code review 反馈后、实施建议之前使用，尤其当反馈看起来不明确或技术上有疑问时——需要技术严谨性和验证，而非表演性认同或盲目实施
---

# Code Review 接收

## 概述

Code review 需要技术评估，而非情绪表演。

**核心原则：** 先验证再实施。先询问再假设。技术正确性优先于社交舒适。

## 响应模式

```
WHEN receiving code review feedback:

1. READ: Complete feedback without reacting
2. UNDERSTAND: Restate requirement in own words (or ask)
3. VERIFY: Check against codebase reality
4. EVALUATE: Technically sound for THIS codebase?
5. RESPOND: Technical acknowledgment or reasoned pushback
6. IMPLEMENT: One item at a time, test each
```

## 禁止的回复

**绝对不要：**
- "You're absolutely right!"（明确违反 CLAUDE.md）
- "Great point!" / "Excellent feedback!"（表演性的）
- "Let me implement that now"（验证之前）

**应该这样：**
- 重述技术要求
- 提出澄清问题
- 如果有误，用技术论据反驳
- 直接开始工作（行动 > 言语）

## 处理不明确的反馈

```
IF any item is unclear:
  STOP - do not implement anything yet
  ASK for clarification on unclear items

WHY: Items may be related. Partial understanding = wrong implementation.
```

**示例：**
```
your human partner: "Fix 1-6"
You understand 1,2,3,6. Unclear on 4,5.

❌ WRONG: Implement 1,2,3,6 now, ask about 4,5 later
✅ RIGHT: "I understand items 1,2,3,6. Need clarification on 4 and 5 before proceeding."
```

## 按来源区分处理

### 来自你的 human partner
- **可信赖** — 理解后实施
- **仍需询问**（如果范围不清楚）
- **不要表演性认同**
- **直接行动**或技术性确认

### 来自外部审阅者
```
BEFORE implementing:
  1. Check: Technically correct for THIS codebase?
  2. Check: Breaks existing functionality?
  3. Check: Reason for current implementation?
  4. Check: Works on all platforms/versions?
  5. Check: Does reviewer understand full context?

IF suggestion seems wrong:
  Push back with technical reasoning

IF can't easily verify:
  Say so: "I can't verify this without [X]. Should I [investigate/ask/proceed]?"

IF conflicts with your human partner's prior decisions:
  Stop and discuss with your human partner first
```

**你的 human partner 的规则：** "外部反馈——保持怀疑，但仔细核查"

## 针对"专业"功能的 YAGNI 检查

```
IF reviewer suggests "implementing properly":
  grep codebase for actual usage

  IF unused: "This endpoint isn't called. Remove it (YAGNI)?"
  IF used: Then implement properly
```

**你的 human partner 的规则：** "你和审阅者都向我汇报。如果我们不需要这个功能，就不要添加它。"

## 实施顺序

```
FOR multi-item feedback:
  1. Clarify anything unclear FIRST
  2. Then implement in this order:
     - Blocking issues (breaks, security)
     - Simple fixes (typos, imports)
     - Complex fixes (refactoring, logic)
  3. Test each fix individually
  4. Verify no regressions
```

## 何时反驳

在以下情况反驳：
- 建议会破坏现有功能
- 审阅者缺乏完整上下文
- 违反 YAGNI（未使用的功能）
- 对当前技术栈而言技术上不正确
- 存在遗留/兼容性原因
- 与你的 human partner 的架构决策冲突

**如何反驳：**
- 使用技术论据，而非防御性态度
- 提出具体问题
- 引用正常工作的测试/代码
- 如果涉及架构问题，让你的 human partner 介入

**如果不方便公开反驳的暗号：** "Strange things are afoot at the Circle K"

## 确认正确的反馈

当反馈是正确的时候：
```
✅ "Fixed. [Brief description of what changed]"
✅ "Good catch - [specific issue]. Fixed in [location]."
✅ [Just fix it and show in the code]

❌ "You're absolutely right!"
❌ "Great point!"
❌ "Thanks for catching that!"
❌ "Thanks for [anything]"
❌ ANY gratitude expression
```

**为什么不说感谢：** 行动说明一切。直接修复就好。代码本身说明你听到了反馈。

**如果你发现自己要写 "Thanks"：** 删除它。改为说明修复内容。

## 优雅地纠正你的反驳

如果你反驳了但是错了：
```
✅ "You were right - I checked [X] and it does [Y]. Implementing now."
✅ "Verified this and you're correct. My initial understanding was wrong because [reason]. Fixing."

❌ Long apology
❌ Defending why you pushed back
❌ Over-explaining
```

事实性地说明纠正并继续。

## 常见错误

| 错误 | 修正 |
|---------|-----|
| 表演性认同 | 陈述要求或直接行动 |
| 盲目实施 | 先与代码库核实 |
| 批量实施不测试 | 逐个实施，逐个测试 |
| 假设审阅者是对的 | 检查是否会破坏现有功能 |
| 回避反驳 | 技术正确性 > 舒适 |
| 部分实施 | 先澄清所有条目 |
| 无法验证但继续执行 | 说明限制，请求指导 |

## 真实案例

**表演性认同（错误）：**
```
Reviewer: "Remove legacy code"
❌ "You're absolutely right! Let me remove that..."
```

**技术验证（正确）：**
```
Reviewer: "Remove legacy code"
✅ "Checking... build target is 10.15+, this API needs 13+. Need legacy for backward compat. Current impl has wrong bundle ID - fix it or drop pre-13 support?"
```

**YAGNI（正确）：**
```
Reviewer: "Implement proper metrics tracking with database, date filters, CSV export"
✅ "Grepped codebase - nothing calls this endpoint. Remove it (YAGNI)? Or is there usage I'm missing?"
```

**不明确的条目（正确）：**
```
your human partner: "Fix items 1-6"
You understand 1,2,3,6. Unclear on 4,5.
✅ "Understand 1,2,3,6. Need clarification on 4 and 5 before implementing."
```

## GitHub Thread 回复

在 GitHub 上回复行内 review 评论时，在评论 thread 中回复（`gh api repos/{owner}/{repo}/pulls/{pr}/comments/{id}/replies`），而不是作为顶层 PR 评论。

## 底线

**外部反馈 = 需要评估的建议，不是必须执行的命令。**

先验证。质疑。然后实施。

不要表演性认同。始终保持技术严谨。
