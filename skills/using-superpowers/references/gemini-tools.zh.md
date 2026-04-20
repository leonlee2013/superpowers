# Gemini CLI 工具映射

Skill 使用 Claude Code 的工具名称。当你在 skill 中遇到这些名称时，请使用你平台的对应工具：

| Skill 引用 | Gemini CLI 对应工具 |
|------------|-------------------|
| `Read`（文件读取） | `read_file` |
| `Write`（文件创建） | `write_file` |
| `Edit`（文件编辑） | `replace` |
| `Bash`（运行命令） | `run_shell_command` |
| `Grep`（搜索文件内容） | `grep_search` |
| `Glob`（按名称搜索文件） | `glob` |
| `TodoWrite`（任务跟踪） | `write_todos` |
| `Skill` 工具（调用 skill） | `activate_skill` |
| `WebSearch` | `google_web_search` |
| `WebFetch` | `web_fetch` |
| `Task` 工具（分派 subagent） | 无对应工具——Gemini CLI 不支持 subagent |

## 不支持 subagent

Gemini CLI 没有与 Claude Code 的 `Task` 工具等效的功能。依赖 subagent 分派的 skill（`subagent-driven-development`、`dispatching-parallel-agents`）将回退到通过 `executing-plans` 进行单会话执行。

## Gemini CLI 附加工具

以下工具在 Gemini CLI 中可用，但在 Claude Code 中没有对应：

| 工具 | 用途 |
|------|------|
| `list_directory` | 列出文件和子目录 |
| `save_memory` | 将事实持久化到 GEMINI.md 以跨会话使用 |
| `ask_user` | 向用户请求结构化输入 |
| `tracker_create_task` | 丰富的任务管理（创建、更新、列出、可视化） |
| `enter_plan_mode` / `exit_plan_mode` | 在进行更改前切换到只读研究模式 |
