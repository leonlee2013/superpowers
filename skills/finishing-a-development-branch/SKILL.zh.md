---
name: finishing-a-development-branch
description: 当实现完成、所有测试通过，需要决定如何集成工作时使用——通过展示合并、PR 或清理的结构化选项来指导开发工作的收尾
---

# 完成开发分支

## 概述

通过展示清晰的选项并处理所选工作流，指导开发工作的收尾。

**核心原则：** 验证测试 -> 展示选项 -> 执行选择 -> 清理。

**开始时宣布：** "我正在使用 finishing-a-development-branch skill 来完成此工作。"

## 流程

### 步骤 1：验证测试

**在展示选项之前，验证测试通过：**

```bash
# 运行项目的测试套件
npm test / cargo test / pytest / go test ./...
```

**如果测试失败：**
```
Tests failing (<N> failures). Must fix before completing:

[Show failures]

Cannot proceed with merge/PR until tests pass.
```

停止。不要进入步骤 2。

**如果测试通过：** 继续步骤 2。

### 步骤 2：确定基础分支

```bash
# 尝试常见的基础分支
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

或者询问："This branch split from main - is that correct?"

### 步骤 3：展示选项

展示恰好这 4 个选项：

```
Implementation complete. What would you like to do?

1. Merge back to <base-branch> locally
2. Push and create a Pull Request
3. Keep the branch as-is (I'll handle it later)
4. Discard this work

Which option?
```

**不要添加解释** — 保持选项简洁。

### 步骤 4：执行选择

#### 选项 1：本地合并

```bash
# 切换到基础分支
git checkout <base-branch>

# 拉取最新代码
git pull

# 合并功能分支
git merge <feature-branch>

# 在合并结果上验证测试
<test command>

# 如果测试通过
git branch -d <feature-branch>
```

然后：清理 worktree（步骤 5）

#### 选项 2：推送并创建 PR

```bash
# 推送分支
git push -u origin <feature-branch>

# 创建 PR
gh pr create --title "<title>" --body "$(cat <<'EOF'
## Summary
<2-3 bullets of what changed>

## Test Plan
- [ ] <verification steps>
EOF
)"
```

然后：清理 worktree（步骤 5）

#### 选项 3：保持原样

报告："Keeping branch <name>. Worktree preserved at <path>."

**不要清理 worktree。**

#### 选项 4：丢弃

**先确认：**
```
This will permanently delete:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path>

Type 'discard' to confirm.
```

等待精确确认。

如果确认：
```bash
git checkout <base-branch>
git branch -D <feature-branch>
```

然后：清理 worktree（步骤 5）

### 步骤 5：清理 Worktree

**对于选项 1、2、4：**

检查是否在 worktree 中：
```bash
git worktree list | grep $(git branch --show-current)
```

如果是：
```bash
git worktree remove <worktree-path>
```

**对于选项 3：** 保留 worktree。

## 快速参考

| 选项 | 合并 | 推送 | 保留 Worktree | 清理分支 |
|--------|-------|------|---------------|----------------|
| 1. 本地合并 | ✓ | - | - | ✓ |
| 2. 创建 PR | - | ✓ | ✓ | - |
| 3. 保持原样 | - | - | ✓ | - |
| 4. 丢弃 | - | - | - | ✓ (强制) |

## 常见错误

**跳过测试验证**
- **问题：** 合并有问题的代码，创建失败的 PR
- **修正：** 在提供选项之前始终验证测试

**开放式问题**
- **问题：** "接下来该怎么做？" -> 含糊不清
- **修正：** 展示恰好 4 个结构化选项

**自动清理 worktree**
- **问题：** 在可能还需要的情况下移除 worktree（选项 2、3）
- **修正：** 仅在选项 1 和 4 时清理

**丢弃前不确认**
- **问题：** 意外删除工作成果
- **修正：** 要求输入 "discard" 确认

## 红线

**绝对不要：**
- 在测试失败的情况下继续
- 在未验证合并结果测试的情况下合并
- 在未确认的情况下删除工作成果
- 在未经明确要求的情况下 force-push

**始终要：**
- 在提供选项之前验证测试
- 展示恰好 4 个选项
- 对选项 4 获取输入确认
- 仅在选项 1 和 4 时清理 worktree

## 集成

**被调用方：**
- **subagent-driven-development**（步骤 7）— 在所有任务完成后
- **executing-plans**（步骤 5）— 在所有批次完成后

**配合使用：**
- **using-git-worktrees** — 清理由该 skill 创建的 worktree
