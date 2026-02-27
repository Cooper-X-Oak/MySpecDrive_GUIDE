---
name: openspec-propose
description: 一键生成完整 OpenSpec 提案。当用户想要快速描述需求，并一次性生成 Proposal, Design, Specs 和 Tasks 时使用。
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.2.0"
---

## 💡 初学者指南 (Beginner's Guide)

此 Skill 适合快速启动。与 `openspec-new-change` 不同，它不会一步步停下来问你，而是根据你的描述，尝试一次性生成所有规划文档（Proposal/Specs/Design/Tasks）。

- **何时使用**: 当你对需求非常清楚，且变更较小，不需要每一步都人工审核时。
- **功能**: 输入需求描述，输出完整的文档链。
- **下一步**: 生成完成后，使用 `openspec-apply-change` 开始写代码。

---

Propose a new change - create the change and generate all artifacts in one step.

I'll create a change with artifacts:
- proposal.md (what & why)
- design.md (how)
- tasks.md (implementation steps)

When ready to implement, run /opsx:apply

---

**Input**: The user's request should include a change name (kebab-case) OR a description of what they want to build.

**Steps**

1. **如果没有提供明确输入，询问用户想做什么 (If no clear input provided, ask what they want to build)**

   使用 **AskUserQuestion tool** (开放式问题，无预设选项) 提问:
   > "What change do you want to work on? Describe what you want to build or fix."

   根据他们的描述，推导出一个 kebab-case 格式的名称 (例如 "add user authentication" → `add-user-auth`)。

   **IMPORTANT**: Do NOT proceed without understanding what the user wants to build.

2. **创建变更目录 (Create the change directory)**
   ```bash
   openspec new change "<name>"
   ```
   这将创建 `openspec/changes/<name>/` 目录和 `.openspec.yaml`。

3. **获取工件构建顺序 (Get the artifact build order)**
   ```bash
   openspec status --change "<name>" --json
   ```
   解析 JSON 以获取:
   - `applyRequires`: 实施前需要的工件 ID 数组 (例如 `["tasks"]`)
   - `artifacts`: 所有工件及其状态和依赖关系的列表

4. **按顺序创建工件直到可以实施 (Create artifacts in sequence until apply-ready)**

   使用 **TodoWrite tool** 跟踪工件进度。

   按依赖顺序循环遍历工件 (没有待处理依赖的工件优先):

   a. **对于每个 `ready` (依赖已满足) 的工件**:
      - 获取指引:
        ```bash
        openspec instructions <artifact-id> --change "<name>" --json
        ```
      - 指引 JSON 包括:
        - `context`: 项目背景 (constraints for you - do NOT include in output)
        - `rules`: 工件特定规则 (constraints for you - do NOT include in output)
        - `template`: 用于输出文件的结构模板
        - `instruction`: 该工件类型的特定 Schema 指导
        - `outputPath`: 写入工件的位置
        - `dependencies`: 读取上下文的已完成工件
      - 读取任何已完成的依赖文件以获取上下文
      - 使用 `template` 作为输出文件的结构
      - 应用 `context` 和 `rules` 作为约束 - 但不要将它们复制到文件中
      - 显示简要进度: "Created <artifact-id>"

   b. **继续直到所有 `applyRequires` 工件完成**
      - 创建每个工件后，重新运行 `openspec status --change "<name>" --json`
      - 检查 `applyRequires` 中的每个工件 ID 是否在 artifacts 数组中具有 `status: "done"`
      - 当所有 `applyRequires` 工件完成时停止

   c. **如果工件需要用户输入** (上下文不清楚):
      - 使用 **AskUserQuestion tool** 进行澄清
      - 然后继续创建

5. **显示最终状态 (Show final status)**
   ```bash
   openspec status --change "<name>"
   ```

**Output**

完成所有工件后，总结:
- 变更名称和位置
- 已创建工件的列表及其简要描述
- 准备就绪: "All artifacts created! Ready for implementation."
- 提示: "Run `/opsx:apply` or ask me to implement to start working on the tasks."

**Artifact Creation Guidelines**

- 遵循每个工件类型的 `openspec instructions` 中的 `instruction` 字段
- Schema 定义了每个工件应包含的内容 - 请遵循它
- 在创建新工件之前读取依赖工件以获取上下文
- 使用 `template` 作为输出文件的结构 - 填充其部分
