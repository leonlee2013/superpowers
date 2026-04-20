---
name: executing-plans
description: 当你有一份书面实现计划需要在独立会话中执行并设置审阅检查点时使用
---

# 执行计划

## 概述

加载计划，批判性审阅，执行所有任务，完成后汇报。

**开始时宣布：** "我正在使用 executing-plans skill 来实现此计划。"

**注意：** 告诉你的 human partner，Superpowers 在有 subagent 支持时效果好得多。如果在支持 subagent 的平台上运行（例如 Claude Code 或 Codex），其工作质量将显著提高。如果 subagent 可用，请使用 superpowers:subagent-driven-development 而不是此 skill。

## 流程

### 步骤 1：加载并审阅计划
1. 读取计划文件
2. 批判性审阅——找出计划中的任何问题或疑虑
3. 如果有疑虑：在开始之前向你的 human partner 提出
4. 如果没有疑虑：创建 TodoWrite 并继续

### 步骤 2：执行任务

对于每个任务：
1. 标记为 in_progress
2. 严格按照每个步骤执行（计划中有细粒度的步骤）
3. 按规定运行验证
4. 标记为 completed

### 步骤 3：完成开发

所有任务完成并验证后：
- 宣布："我正在使用 finishing-a-development-branch skill 来完成此工作。"
- **必须调用的子 skill：** 使用 superpowers:finishing-a-development-branch
- 按照该 skill 的指引验证测试、展示选项、执行选择

## 何时停下来寻求帮助

**在以下情况立即停止执行：**
- 遇到阻塞（缺少依赖、测试失败、指令不明确）
- 计划有严重缺陷导致无法开始
- 你不理解某条指令
- 验证反复失败

**先请求澄清，不要猜测。**

## 何时回到前面的步骤

**在以下情况返回审阅（步骤 1）：**
- partner 根据你的反馈更新了计划
- 基本方案需要重新思考

**不要硬闯阻塞** — 停下来询问。

## 要点

- 先批判性审阅计划
- 严格按照计划步骤执行
- 不要跳过验证
- 当计划要求使用 skill 时引用相应 skill
- 遇到阻塞时停下来，不要猜测
- 未经用户明确同意，绝不在 main/master 分支上开始实现

## 集成

**必需的工作流 skill：**
- **superpowers:using-git-worktrees** — 必需：在开始之前设置隔离的工作空间
- **superpowers:writing-plans** — 创建此 skill 执行的计划
- **superpowers:finishing-a-development-branch** — 在所有任务完成后完成开发工作
