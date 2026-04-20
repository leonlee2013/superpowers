# Codex 工具映射

Skill 使用 Claude Code 的工具名称。当你在 skill 中遇到这些名称时，请使用你平台的对应工具：

| Skill 引用 | Codex 对应工具 |
|------------|---------------|
| `Task` 工具（分派 subagent） | `spawn_agent`（参见 [命名 agent 分派](#named-agent-dispatch)） |
| 多个 `Task` 调用（并行） | 多个 `spawn_agent` 调用 |
| Task 返回结果 | `wait` |
| Task 自动完成 | `close_agent` 释放槽位 |
| `TodoWrite`（任务跟踪） | `update_plan` |
| `Skill` 工具（调用 skill） | Skill 原生加载——直接按照指令操作 |
| `Read`、`Write`、`Edit`（文件操作） | 使用你的原生文件工具 |
| `Bash`（运行命令） | 使用你的原生 shell 工具 |

## Subagent 分派需要多 agent 支持

在你的 Codex 配置（`~/.codex/config.toml`）中添加：

```toml
[features]
multi_agent = true
```

这将启用 `spawn_agent`、`wait` 和 `close_agent`，用于 `dispatching-parallel-agents` 和 `subagent-driven-development` 等 skill。

## 命名 agent 分派

Claude Code 的 skill 引用命名 agent 类型，如 `superpowers:code-reviewer`。
Codex 没有命名 agent 注册表——`spawn_agent` 从内置角色（`default`、`explorer`、`worker`）创建通用 agent。

当 skill 要求分派命名 agent 类型时：

1. 找到 agent 的提示词文件（例如 `agents/code-reviewer.md` 或 skill 的
   本地提示词模板如 `code-quality-reviewer-prompt.md`）
2. 读取提示词内容
3. 填充所有模板占位符（`{BASE_SHA}`、`{WHAT_WAS_IMPLEMENTED}` 等）
4. 使用填充后的内容作为 `message` 参数生成一个 `worker` agent

| Skill 指令 | Codex 对应工具 |
|-----------|---------------|
| `Task tool (superpowers:code-reviewer)` | `spawn_agent(agent_type="worker", message=...)` 使用 `code-reviewer.md` 的内容 |
| `Task tool (general-purpose)` 带内联提示词 | `spawn_agent(message=...)` 使用相同的提示词 |

### 消息框架

`message` 参数是用户级输入，而非系统提示词。按以下结构组织以最大化指令遵从度：

```
Your task is to perform the following. Follow the instructions below exactly.

<agent-instructions>
[从 agent 的 .md 文件中填充的提示词内容]
</agent-instructions>

Execute this now. Output ONLY the structured response following the format
specified in the instructions above.
```

- 使用任务委派框架（"Your task is..."）而非角色框架（"You are..."）
- 将指令包裹在 XML 标签中——模型会将标签包裹的内容块视为权威指令
- 以明确的执行指令结尾，防止模型只是总结指令内容

### 何时可以移除此变通方案

此方法是为了弥补 Codex 的插件系统尚不支持 `plugin.json` 中的 `agents`
字段。当 `RawPluginManifest` 增加 `agents` 字段后，插件可以创建指向 `agents/` 的符号链接（类似现有的 `skills/` 符号链接），skill 就可以直接分派命名 agent 类型。

## 环境检测

创建 worktree 或完成分支的 skill 应在执行前通过只读 git 命令检测其环境：

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

- `GIT_DIR != GIT_COMMON` → 已在链接的 worktree 中（跳过创建）
- `BRANCH` 为空 → 处于分离 HEAD 状态（无法从沙箱中进行分支/推送/PR 操作）

参见 `using-git-worktrees` 步骤 0 和 `finishing-a-development-branch`
步骤 1 了解每个 skill 如何使用这些信号。

## Codex App 完成流程

当沙箱阻止分支/推送操作时（在外部管理的 worktree 中处于分离 HEAD 状态），agent 会提交所有工作并告知用户使用 App 的原生控件：

- **"Create branch"** —— 命名分支，然后通过 App UI 进行 commit/push/PR
- **"Hand off to local"** —— 将工作转移到用户的本地检出

Agent 仍然可以运行测试、暂存文件，并输出建议的分支名称、commit 消息和 PR 描述供用户复制使用。
