---
name: openspec-continue-change
description: 继续进行 OpenSpec 变更，创建下一个工件。当用户想要推进变更进度、生成下一个文档或继续工作流时使用。
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.2.0"
---

## 💡 初学者指南 (Beginner's Guide)

此 Skill 用于推进工作流。在 OpenSpec 模式下，我们不会一次性生成所有文档，而是分步骤进行（如 Proposal -> Specs -> Design -> Tasks）。当你完成一步审查后，使用此 Skill 继续下一步。

- **何时使用**: 当你完成了 Proposal 并想生成 Specs，或者完成了 Design 并想生成 Tasks 时。
- **前置条件**: 上一个工件必须已经存在。
- **操作**: 它会自动读取上一步的输出作为上下文，为你生成当前步骤的草稿。

---

Continue working on a change by creating the next artifact.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **如果没有提供变更名称，提示选择 (If no change name provided, prompt for selection)**

   运行 `openspec list --json` 获取按修改时间排序的可用变更。然后使用 **AskUserQuestion tool** 让用户选择要处理哪个变更。

   展示最近修改的前 3-4 个变更作为选项，显示:
   - Change name
   - Schema (如果有 `schema` 字段，否则默认为 "spec-driven")
   - Status (例如 "0/5 tasks", "complete", "no tasks")
   - 最近修改时间 (从 `lastModified` 字段获取)

   将最近修改的变更标记为 "(Recommended)"，因为它很可能是用户想要继续的。

   **IMPORTANT**: Do NOT guess or auto-select a change. Always let the user choose.

2. **检查当前状态 (Check current status)**
   ```bash
   openspec status --change "<name>" --json
   ```
   解析 JSON 以了解当前状态。响应包括:
   - `schemaName`: 正在使用的工作流 schema (例如 "spec-driven")
   - `artifacts`: 工件数组及其状态 ("done", "ready", "blocked")
   - `isComplete`: 布尔值，指示所有工件是否已完成

3. **根据状态采取行动 (Act based on status)**:

   ---

   **如果所有工件已完成 (`isComplete: true`)**:
   - 祝贺用户
   - 显示最终状态，包括使用的 schema
   - 建议: "All artifacts created! You can now implement this change or archive it." (所有工件已创建！你现在可以实施此变更或将其归档。)
   - STOP

   ---

   **如果有工件准备好创建 (If artifacts are ready to create)** (status 显示有 `status: "ready"` 的工件):
   - 从 status 输出中选择 **第一个** `status: "ready"` 的工件
   - 获取其指引:
     ```bash
     openspec instructions <artifact-id> --change "<name>" --json
     ```
   - 解析 JSON。关键字段有:
     - `context`: 项目背景 (constraints for you - do NOT include in output)
     - `rules`: 工件特定规则 (constraints for you - do NOT include in output)
     - `template`: 用于输出文件的结构模板
     - `instruction`: Schema 特定的指导
     - `outputPath`: 写入工件的位置
     - `dependencies`: 读取上下文的已完成工件
   - **创建工件文件 (Create the artifact file)**:
     - 读取任何已完成的依赖文件以获取上下文
     - 使用 `template` 作为结构 - 填充其部分
     - 应用 `context` 和 `rules` 作为约束 - 但不要将它们复制到文件中
     - 写入 instructions 中指定的输出路径
   - 显示已创建的内容以及现在解锁的内容
   - 创建 **一个** 工件后 STOP

   ---

   **如果没有工件准备好 (所有都受阻) (If no artifacts are ready)**:
   - 这在有效 schema 下不应发生
   - 显示状态并建议检查问题

4. **创建工件后，显示进度 (After creating an artifact, show progress)**
   ```bash
   openspec status --change "<name>"
   ```

**Output**

每次调用后显示:
- 创建了哪个工件
- 正在使用的 Schema 工作流
- 当前进度 (N/M complete)
- 现在解锁了哪些工件
- 提示语: "Want to continue? Just ask me to continue or tell me what to do next." (想继续吗？只需让我继续或告诉我下一步做什么。)

**Artifact Creation Guidelines**

工件类型及其用途取决于 schema。使用 instructions 输出中的 `instruction` 字段来了解要创建什么。

常见工件模式:

**spec-driven schema** (proposal → specs → design → tasks):
- **proposal**: 变更意图的高层描述。
- **specs**: 详细的行为变更 (delta specs)。
- **design**: 技术实现计划。
- **tasks**: 实施清单。
