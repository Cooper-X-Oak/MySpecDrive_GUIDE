---
name: verify-suite
description: Run comprehensive project verification (Lint, Type, E2E). Use when user wants to verify changes, run tests, or ensure quality before release.
---

## 💡 初学者指南 (Beginner's Guide)

此 Skill 是你的“质量安检员”。

- **何时使用**: 在提交代码前，或者想要确保代码没有低级错误（Lint/Type）和逻辑错误（Test）时。
- **功能**: 自动运行项目中的 Lint（代码风格）、Type Check（类型检查）和 Test（单元/E2E 测试），确保项目质量符合标准。

---

# Verify Suite

This skill runs the project's verification suite to ensure code quality and functionality.

## Verification Levels

Determine the verification level based on user request or change magnitude:

### 1. Quick Verify (Lint & Types)

Run this for small code changes.

```bash
npm run lint
npm run type-check
```

_(Note: If `type-check` script is missing, use `npx tsc --noEmit`)_

### 2. Functional Verify (Unit & Logic)

Run this for logic changes.

```bash
npm run test
```

### 3. Full Verify (E2E & Visual)

Run this for UI changes, layout changes, or before major commits.
**Scripts Location**: `scripts/verify/`

Recommended sequence:

1.  **Build** (if needed for E2E): `npm run build`
2.  **E2E Test**: `npm run test:e2e` (or `npx tsx scripts/verify/test-e2e.ts`)
3.  **Visual Verification**: `npx tsx scripts/verify/verify-visuals.ts`
4.  **Export Verification**: `npx tsx scripts/verify/verify-export.ts`

## Automated Workflow

1.  **Identify Context**: Check which files were modified.
    - If `*.tsx` / `*.css`: Suggest **Full Verify**.
    - If `*.ts` (logic): Suggest **Functional Verify**.
    - If docs/minor: Suggest **Quick Verify**.

2.  **Execute**: Run the selected commands.
    - Always stop if a step fails.
    - Report the error clearly.

3.  **Report**: Summarize the verification results.
    - ✅ All passed
    - ❌ Failed at <step>: <error>
