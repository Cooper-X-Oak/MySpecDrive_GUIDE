---
name: openspec-verify-change
description: 验证实现是否与变更工件匹配。当用户想要在归档前验证实现是否完整、正确且一致时使用。
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.2.0"
---

## 💡 初学者指南 (Beginner's Guide)

此 Skill 用于质量检查。在代码写完后，归档前，我们必须验证实现是否符合最初的 Specs 和 Design。

- **何时使用**: 当所有代码任务都已完成（`openspec-apply-change` 跑完了），但在归档之前。
- **功能**: 它会根据 `specs/` 中的需求和 `tasks.md` 中的清单，检查代码库是否真正实现了所有功能。
- **输出**: 一份验证报告，指出缺失的功能、潜在的错误或设计偏差。

---

Verify that an implementation matches the change artifacts (specs, tasks, design).

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **如果没有提供变更名称，提示选择 (If no change name provided, prompt for selection)**

   运行 `openspec list --json` 获取可用变更。使用 **AskUserQuestion tool** 让用户选择。

   显示有实施任务的变更 (tasks artifact exists)。
   如果有可用 schema，包含每个变更的 schema。
   将未完成任务的变更标记为 "(In Progress)"。

   **IMPORTANT**: Do NOT guess or auto-select a change. Always let the user choose.

2. **检查状态以了解 Schema (Check status to understand the schema)**
   ```bash
   openspec status --change "<name>" --json
   ```
   解析 JSON 以了解:
   - `schemaName`: 正在使用的工作流 (例如 "spec-driven")
   - 该变更存在哪些工件

3. **获取变更目录并加载工件 (Get the change directory and load artifacts)**

   ```bash
   openspec instructions apply --change "<name>" --json
   ```

   这会返回变更目录和上下文文件。从 `contextFiles` 读取所有可用工件。

4. **初始化验证报告结构 (Initialize verification report structure)**

   创建一个包含三个维度的报告结构:
   - **完整性 (Completeness)**: 跟踪任务和 spec 覆盖率
   - **正确性 (Correctness)**: 跟踪需求实现和场景覆盖率
   - **一致性 (Coherence)**: 跟踪设计依从性和模式一致性

   每个维度可以有 CRITICAL (严重), WARNING (警告), 或 SUGGESTION (建议) 问题。

5. **验证完整性 (Verify Completeness)**

   **任务完成情况 (Task Completion)**:
   - 如果 contextFiles 中存在 tasks.md，读取它
   - 解析复选框: `- [ ]` (未完成) vs `- [x]` (已完成)
   - 统计完成 vs 总任务数
   - 如果存在未完成任务:
     - 为每个未完成任务添加 CRITICAL 问题
     - 建议: "Complete task: <description>" 或 "Mark as done if already implemented"

   **Spec 覆盖率 (Spec Coverage)**:
   - 如果 `openspec/changes/<name>/specs/` 中存在 delta specs:
     - 提取所有需求 (标记为 "### Requirement:")
     - 对于每个需求:
       - 在代码库中搜索与需求相关的关键词
       - 评估是否可能存在实现
     - 如果需求似乎未实现:
       - 添加 CRITICAL 问题: "Requirement not found: <requirement name>"
       - 建议: "Implement requirement X: <description>"

6. **验证正确性 (Verify Correctness)**

   **需求实现映射 (Requirement Implementation Mapping)**:
   - 对于来自 delta specs 的每个需求:
     - 搜索代码库以获取实现证据
     - 如果找到，记录文件路径和行范围
     - 评估实现是否匹配需求意图
     - 如果检测到偏差:
       - 添加 WARNING: "Implementation may diverge from spec: <details>"
       - 建议: "Review <file>:<lines> against requirement X"

   **场景覆盖率 (Scenario Coverage)**:
   - 对于 delta specs 中的每个场景 (标记为 "#### Scenario:"):
     - 检查代码中是否处理了条件
     - 检查是否存在覆盖该场景的测试
     - 如果场景似乎未覆盖:
       - 添加 WARNING: "Scenario not covered: <scenario name>"
       - 建议: "Add test or implementation for scenario: <description>"

7. **验证一致性 (Verify Coherence)**

   **设计依从性 (Design Adherence)**:
   - 如果 contextFiles 中存在 design.md:
     - 提取关键决策 (查找 "Decision:", "Approach:", "Architecture:" 等部分)
     - 验证实现是否遵循这些决策
     - 如果检测到矛盾:
       - 添加 WARNING: "Design decision not followed: <decision>"
       - 建议: "Update implementation or revise design.md to match reality"
