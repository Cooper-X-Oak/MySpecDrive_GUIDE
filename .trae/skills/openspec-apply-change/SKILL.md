---
name: openspec-apply-change
description: 实施 OpenSpec 变更中的任务。当用户想要开始写代码、继续实现或处理任务列表时使用。
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.2.0"
---

## 💡 初学者指南 (Beginner's Guide)

此 Skill 用于执行具体的代码实现。当你的设计文档（Proposal/Specs/Design/Tasks）都已准备就绪并通过审查后，使用此 Skill 让 AI 开始根据 Tasks 列表编写代码。

- **何时使用**: 当所有规划文档（Artifacts）都已完成（状态为 `all_done`），准备开始写代码时。
- **功能**: 它会读取 `tasks.md`，逐个任务进行实现，并自动更新任务状态（打钩）。
- **注意**: 它会自动处理依赖文件读取和上下文理解。

---

Implement tasks from an OpenSpec change.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **选择变更 (Select the change)**

   如果提供了名称，使用它。否则:
   - 如果用户提到了变更，从对话上下文中推断
   - 如果只有一个活跃变更，自动选择
   - 如果有歧义，运行 `openspec list --json` 获取可用变更并使用 **AskUserQuestion tool** 让用户选择

   始终声明: "Using change: <name>" 以及如何覆盖 (例如 `/opsx:apply <other>`)。

2. **检查状态以了解 Schema (Check status to understand the schema)**
   ```bash
   openspec status --change "<name>" --json
   ```
   解析 JSON 以了解:
   - `schemaName`: 正在使用的工作流 (例如 "spec-driven")
   - 哪个工件包含任务 (通常 spec-driven 为 "tasks"，其他 schema 请检查 status)

3. **获取实施指引 (Get apply instructions)**

   ```bash
   openspec instructions apply --change "<name>" --json
   ```

   这会返回:
   - 上下文文件路径 (因 schema 而异 - 可能是 proposal/specs/design/tasks 或 spec/tests/implementation/docs)
   - 进度 (总数, 完成数, 剩余数)
   - 带有状态的任务列表
   - 基于当前状态的动态指引

   **处理状态 (Handle states):**
   - 如果 `state: "blocked"` (缺少工件): 显示消息，建议使用 openspec-continue-change
   - 如果 `state: "all_done"`: 祝贺，建议归档 (archive)
   - 否则: 继续实施

4. **读取上下文文件 (Read context files)**

   读取 apply instructions 输出中 `contextFiles` 列出的文件。
   文件取决于正在使用的 schema:
   - **spec-driven**: proposal, specs, design, tasks
   - 其他 schemas: 遵循 CLI 输出的 contextFiles

5. **显示当前进度 (Show current progress)**

   显示:
   - 正在使用的 Schema
   - 进度: "N/M tasks complete"
   - 剩余任务概览
   - 来自 CLI 的动态指引

6. **实施任务 (循环直到完成或受阻) (Implement tasks (loop until done or blocked))**

   对于每个待办任务:
   - 显示正在处理哪个任务
   - 进行所需的代码更改
   - 保持更改最小化且专注
   - 在任务文件中将任务标记为完成: `- [ ]` → `- [x]`
   - 继续下一个任务

   **暂停条件 (Pause if):**
   - 任务不明确 → 询问澄清
   - 实施揭示了设计问题 → 建议更新工件
   - 遇到错误或阻塞 → 报告并等待指导
   - 用户中断

7. **完成或暂停时，显示状态 (On completion or pause, show status)**

   显示:
   - 本次会话完成的任务
   - 总体进度: "N/M tasks complete"
   - 如果全部完成: 建议归档
   - 如果暂停: 解释原因并等待指导

**Output During Implementation**

```
## Implementing: <change-name> (schema: <schema-name>)

Working on task 3/7: <task description>
[...implementation happening...]
✓ Task complete

Working on task 4/7: <task description>
[...implementation happening...]
```
