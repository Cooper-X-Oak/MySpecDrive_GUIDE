# AI Agent Guidelines (AGENTS.md)

此文件包含针对 AI 代理（特别是 Trae）在本项目中工作的核心指令和偏好。请严格遵守。

## 核心工作流规则

1.  **Strict Planning Requirement (严格计划要求)**
    -   必须遵循 "Plan-First"（计划优先）工作流。
    -   在编写任何代码之前，必须先通过 OpenSpec 工件（Proposal/Specs/Design/Tasks）分析并确认需求。
    -   严禁在没有批准的 specs 的情况下实施代码。
    -   所有 OpenSpec 工件（Proposal, Specs, Design, Tasks 等）必须使用 **中文** 编写。

2.  **Strict Confirmation Requirement (严格确认要求)**
    -   在创建更改或提案后，必须 **停止** 并等待用户明确确认，然后才能继续实施。
    -   不要假设批准。彻底验证细节。

3.  **Proactive Proposal Prompting (主动提案询问)**
    -   当检测到 `openspec/changes/` 为空（没有活跃的更改）时，代理应主动询问用户是否需要开始新的 Proposal。
    -   不要让项目处于“无方向”状态。

4.  **Atomic Auto-Commit (原子自动提交)**
    -   代理必须在完成任何逻辑单元（任务/修复/功能）并验证后，**主动** 提交更改，无需等待显式指令。
    -   提交应保持原子性。

5.  **Mandatory Technical Stack Approval (强制性技术栈批准)**
    -   严禁未经授权的技术栈决策。
    -   所有主要架构选择（例如渲染引擎、框架）必须先进行比较研究报告并获得用户明确批准。

## 设计与风格偏好

1.  **Geek/Terminal Style Preference (极客/终端风格偏好)**
    -   优先级：效率 > 美学。
    -   偏好 "Geek Style"（例如 Terminal UI, TUI）而非复杂的 GUI。
    -   功能应强大但极简。
    -   偏好具有高 "Cool Factor"（酷炫因子）、创新和以开发者为中心的审美（例如 TUI, Cyberpunk, Keyboard-driven）。

2.  **UI Design Preferences (UI 设计偏好)**
    -   偏好紧凑的 UI 设计（最小的填充/边距）。
    -   数据列表严格使用 **Sans-serif**（无衬线）字体。
    -   除非代码需要，否则序列号避免使用 Monospace（等宽）字体。

## 项目背景

1.  **Career Transition Strategy (职业转型策略)**
    -   用户处于职业转型期（6个月+空窗期），严格优先考虑自我学习、转型和拥有自己的产出（"做我喜欢的事"），而非传统就业（"为他人工作"）。
    -   精力必须投入到自我成长中。

2.  **Hackathon Goal (黑客马拉松目标)**
    -   用户计划参加黑客马拉松。项目应体现创新和技术深度。

## OpenSpec 结构说明

-   `openspec/specs/`: 系统的 "Source of Truth"（当前行为）。
-   `openspec/changes/`: 提议的更改（每个更改一个文件夹）。
    -   `proposal.md`: 意图和范围 (Why & What)。
    -   `design.md`: 技术方法 (How)。
    -   `tasks.md`: 实施检查清单。
    -   `specs/`: Delta specs (变更部分)。
