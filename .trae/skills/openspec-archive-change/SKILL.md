---
name: openspec-archive-change
description: 归档已完成的变更。当用户想要在实施完成后完成并归档变更时使用。
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.2.0"
---

## 💡 初学者指南 (Beginner's Guide)

此 Skill 是工作流的终点。当你完成了所有代码、通过了测试验证，并准备好结束这个任务时使用。

- **功能**:
    1. 检查是否所有任务都已完成。
    2. 检查是否有新的 Specs 需要同步到主 Specs 库（Sync）。
    3. 将整个 `openspec/changes/<name>` 文件夹移动到 `openspec/archive/`，保持项目整洁。
- **输出**: 归档后的记录，方便日后回溯。

---

Archive a completed change in the experimental workflow.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **如果没有提供变更名称，提示选择 (If no change name provided, prompt for selection)**

   运行 `openspec list --json` 获取可用变更。使用 **AskUserQuestion tool** 让用户选择。

   仅显示活跃变更 (未归档的)。
   如果有可用 schema，包含每个变更的 schema。

   **IMPORTANT**: Do NOT guess or auto-select a change. Always let the user choose.

2. **检查工件完成状态 (Check artifact completion status)**

   运行 `openspec status --change "<name>" --json` 检查工件完成情况。

   解析 JSON 以了解:
   - `schemaName`: 正在使用的工作流
   - `artifacts`: 工件列表及其状态 (`done` 或其他)

   **如果有任何工件未完成 (`done`):**
   - 显示列出未完成工件的警告
   - 使用 **AskUserQuestion tool** 确认用户是否想要继续
   - 如果用户确认则继续

3. **检查任务完成状态 (Check task completion status)**

   读取任务文件 (通常是 `tasks.md`) 以检查未完成的任务。

   统计标记为 `- [ ]` (未完成) vs `- [x]` (已完成) 的任务。

   **如果发现未完成的任务:**
   - 显示警告，显示未完成任务的数量
   - 使用 **AskUserQuestion tool** 确认用户是否想要继续
   - 如果用户确认则继续

   **如果不存在任务文件:** 继续，不显示任务相关警告。

4. **评估 Delta Spec 同步状态 (Assess delta spec sync state)**

   检查 `openspec/changes/<name>/specs/` 是否存在 delta specs。如果不存在，继续而不显示同步提示。

   **如果存在 delta specs:**
   - 将每个 delta spec 与其对应的 main spec (`openspec/specs/<capability>/spec.md`) 进行比较
   - 确定将应用哪些更改 (添加, 修改, 删除, 重命名)
   - 在提示之前显示合并摘要

   **提示选项 (Prompt options):**
   - 如果需要更改: "Sync now (recommended)", "Archive without syncing"
   - 如果已同步: "Archive now", "Sync anyway", "Cancel"

   如果用户选择 sync，使用 Task tool (subagent_type: "general-purpose", prompt: "Use Skill tool to invoke openspec-sync-specs for change '<name>'. Delta spec analysis: <include the analyzed delta spec summary>"). 无论选择如何都继续归档。

5. **执行归档 (Perform the archive)**

   如果归档目录不存在则创建:
   ```bash
   mkdir -p openspec/changes/archive
   ```

   使用当前日期生成目标名称: `YYYY-MM-DD-<change-name>`

   **检查目标是否已存在:**
   - 如果是: 报错失败，建议重命名现有归档或使用不同日期
   - 如果否: 将变更目录移动到归档

   ```bash
   mv openspec/changes/<name> openspec/changes/archive/YYYY-MM-DD-<name>
   ```

6. **显示摘要 (Display summary)**

   显示归档完成摘要，包括:
   - 变更名称
   - 使用的 Schema
   - 归档位置
   - 是否同步了 specs (如果适用)
   - 关于任何警告的说明 (未完成的工件/任务)

**Output On Success**

```
## Archive Complete

**Change:** <change-name>
**Schema:** <schema-name>
```
