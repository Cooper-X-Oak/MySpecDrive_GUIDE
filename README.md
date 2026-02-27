# OpenSpec Drive 🚀

[![GitHub](https://img.shields.io/badge/GitHub-MySpecDrive__GUIDE-blue?logo=github)](https://github.com/Cooper-X-Oak/MySpecDrive_GUIDE)
[![OpenSpec](https://img.shields.io/badge/Powered_by-OpenSpec-orange)](https://github.com/fission-ai/openspec)

> **Plan-First, Spec-Driven Development Workflow for AI Coding Assistants**

MySpecDrive 是自用 **Trae IDE** 的 Spec-Driven Development (SDD) 工作流模板。它基于 **[OpenSpec](https://github.com/fission-ai/openspec)** 框架，通过强制执行 "Plan-First"（计划优先）原则和 "Human-in-the-Loop"（人类介入）机制，让 AI 开发者不再“盲目编码”。

希望给同样初学者的你提供学习和部署的便利。

> **🌍 通用性说明 (Universality)**:
> 虽然本项目采用了 Trae IDE 的 `.trae` 配置格式，但其核心的 **Rules (Markdown)** 和 **Skills (Prompts)** 逻辑是通用的。你可以轻松将其 Prompt 移植到 **Cursor** (.cursorrules), **Windsurf**, 或其他支持 System Prompt/Context 的 AI 编程环境中。OpenSpec 是一种方法论，而非仅限于某个工具。

## 🌟 核心理念 (Core Philosophy)

在 AI 辅助编程中，我们常遇到以下问题：
- ❌ AI 一上来就写代码，逻辑漏洞百出。
- ❌ 缺乏全局视野，改了这里坏了那里。
- ❌ 难以进行有效的 Code Review，因为意图不明确。

**OpenSpec Drive** 通过以下原则解决这些问题：

1.  **Plan-First (计划优先)**: 在编写任何代码之前，必须先产出 Proposal（提案）、Specs（规格）、Design（设计）和 Tasks（任务）。
2.  **Human-in-the-Loop (人类介入)**: 在每个关键节点（Proposal -> Specs -> Design -> Implementation），AI 必须暂停并等待人类的审查与批准。
3.  **Artifact-Driven (工件驱动)**: 使用结构化的 Markdown 文档作为 AI 与人类沟通的桥梁，而非仅靠对话。

## 🛠️ 工作流 (The Workflow)

OpenSpec Drive 将开发过程划分为四个严格的阶段，每个阶段都由特定的工件（Artifacts）驱动：

```mermaid
graph TD
    Start((开始)) --> Idea["💡 提出想法"]
    Idea --> |openspec-new-change| Proposal["📄 Proposal (意图与范围)"]
    Proposal --> Check1{"🛑 人类审查"}
    
    Check1 --> |✅ 批准| SpecDesign["🏗️ Specs & Design (逻辑与方案)"]
    SpecDesign --> Check2{"🛑 人类审查"}
    
    Check2 --> |✅ 批准| TaskPlan["📋 Tasks (实施计划)"]
    TaskPlan --> Check3{"🛑 人类审查"}
    
    Check3 --> |✅ 批准| Implement["💻 Implementation (编码与验证)"]
    Implement --> |openspec-verify-change| Check4{"🛑 人类验收"}
    
    Check4 --> |✅ 通过| Archive["📦 Archive (归档)"]
    Archive --> End((完成))
```

### 关键工件 (Artifacts)

- **`proposal.md`**: **Why & What**. 定义变更的意图、背景和范围。
- **`specs/`**: **Requirements**. 系统的行为规范，Source of Truth。
- **`design.md`**: **How**. 技术方案、架构设计、接口定义。
- **`tasks.md`**: **Checklist**. 具体的实施步骤和测试计划。

## 🚀 快速开始 (Getting Started)

### 方式 1: 官方标准安装 (Official)

请直接参考 [OpenSpec 官方文档](https://github.com/fission-ai/openspec#quick-start) 进行初始化。

> 🤖 **Agent 技巧**: 在新项目中，你可以直接告诉你的 AI 助手（如 Trae/Cursor）：
> "请查阅 `https://github.com/fission-ai/openspec` 的 Quick Start，并帮我为当前项目初始化 OpenSpec 框架。"

### 方式 2: 使用本仓库模板 (Recommended for Beginners) 🇨🇳

本仓库是我个人优化过的、**中文友好**的配置版本，更适合国内开发者和初学者。

1. 点击本仓库右上角的 **Code -> Download ZIP** 下载源码包并解压。
2. 将解压文件夹中的 `.trae/` 文件夹复制到你的项目根目录下。

### 3. 开始使用 (Usage)

配置完成后，你的 Trae IDE 将获得一系列 **Skills**。你可以直接与 AI 对话：

- **开始新功能**: "我想开发一个用户登录功能" (自动触发 `openspec-new-change`)
- **继续开发**: "继续下一步" (自动触发 `openspec-continue-change`)
- **验证代码**: "验证一下现在的实现" (自动触发 `openspec-verify-change`)

> 💡 **提示**: OpenSpec 官方也支持使用 `/opsx:propose` 等指令控制 AI，你可以根据喜好选择使用 Trae Skills (自然语言) 或 OpenSpec 指令。

## 🧰 内置技能 (Built-in Skills)

OpenSpec Drive 提供了一系列 Trae Skills 来自动化工作流：

| Skill Name | Description |
| :--- | :--- |
| `openspec-new-change` | 启动一个新的变更流程 (Proposal 阶段) |
| `openspec-continue-change` | 推进工作流到下一阶段 (Specs/Design/Tasks) |
| `openspec-apply-change` | 执行 `tasks.md` 中的实施计划 |
| `openspec-verify-change` | 验证实现是否符合 Specs 和 Tasks |
| `openspec-archive-change` | 完成并归档当前变更 |
| `git-smart-commit` | 智能生成 Conventional Commits 并处理推送 |
| `project-analyze` | 分析项目结构和技术栈 |

## 📜 规则与原则 (Rules)

本项目包含预设的 AI 行为准则 (`.trae/rules/`)：

- **Core-工程化原则**: 强调验证驱动、原子提交和网络健壮性。
- **母语原则**: 强制 AI 使用中文思考和文档编写（代码除外）。

## 🤝 贡献 (Contributing)

欢迎提交 Issue 或 PR 来改进 OpenSpec Drive 工作流！

## 📄 License

MIT License
