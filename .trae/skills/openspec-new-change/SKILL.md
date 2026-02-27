---
name: openspec-new-change
description: 启动一个新的 OpenSpec 变更流程。当用户想要以结构化的步骤创建新功能、修复或修改时使用此工具。
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.2.0"
---

## 💡 初学者指南 (Beginner's Guide)

此 Skill 用于启动一个新的开发任务。它会引导你输入任务名称，并根据 OpenSpec 的规范为你创建相应的工作流文件夹（通常包含 Proposal, Specs, Design, Tasks 等工件）。

- **何时使用**: 当你有一个新想法、Bug 修复或重构需求时。
- **输入**: 你的想法描述（例如 "增加用户登录功能"）。
- **输出**: 一个初始化好的变更目录 `openspec/changes/<name>/` 和第一步的指引。

---

Start a new change using the experimental artifact-driven approach.

**Input**: The user's request should include a change name (kebab-case) OR a description of what they want to build.

**Steps**

1. **如果没有提供明确输入，询问用户想做什么 (If no clear input provided, ask what they want to build)**

   使用 **AskUserQuestion tool** (开放式问题，无预设选项) 提问:
   > "What change do you want to work on? Describe what you want to build or fix." (你想做什么变更？请描述你想构建的功能或修复的问题。)

   根据他们的描述，推导出一个 kebab-case 格式的名称 (例如 "add user authentication" → `add-user-auth`)。

   **IMPORTANT**: Do NOT proceed without understanding what the user wants to build.

2. **确定工作流 Schema (Determine the workflow schema)**

   使用默认 schema (省略 `--schema`)，除非用户明确要求使用不同的工作流。

   **仅当用户提到以下情况时使用不同的 schema:**
   - 指定了 schema 名称 → 使用 `--schema <name>`
   - "show workflows" 或 "what workflows" → 运行 `openspec schemas --json` 并让他们选择

   **否则**: 省略 `--schema` 以使用默认值。

3. **创建变更目录 (Create the change directory)**
   ```bash
   openspec new change "<name>"
   ```
   仅当用户请求特定工作流时添加 `--schema <name>`。
   这将在 `openspec/changes/<name>/` 创建一个包含所选 schema 的脚手架变更。

4. **显示工件状态 (Show the artifact status)**
   ```bash
   openspec status --change "<name>"
   ```
   这会显示哪些工件需要创建，以及哪些已准备好（依赖已满足）。

5. **获取第一个工件的指引 (Get instructions for the first artifact)**
   第一个工件取决于 schema (例如 spec-driven 模式下是 `proposal`)。
   检查 status 输出，找到第一个状态为 "ready" 的工件。
   ```bash
   openspec instructions <first-artifact-id> --change "<name>"
   ```
   这会输出创建第一个工件所需的模板和上下文。

6. **暂停并等待用户指示 (STOP and wait for user direction)**

**Output**

完成步骤后，总结以下内容:
- 变更名称和位置
- 正在使用的 Schema/工作流及其工件序列
- 当前状态 (0/N artifacts complete)
- 第一个工件的模板
- 提示语: "Ready to create the first artifact? Just describe what this change is about and I'll draft it, or ask me to continue." (准备好创建第一个工件了吗？请描述这个变更的内容，我将为你起草，或者直接让我继续。)

**Guardrails**
- Do NOT create any artifacts yet - just show the instructions
- Do NOT advance beyond showing the first artifact template
- If the name is invalid (not kebab-case), ask for a valid name
- If a change with that name already exists, suggest continuing that change instead
- Pass --schema if using a non-default workflow
