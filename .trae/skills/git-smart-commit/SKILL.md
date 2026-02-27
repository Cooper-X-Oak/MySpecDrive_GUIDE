---
name: git-smart-commit
description: 生成符合规范的中文提交信息，并智能处理网络代理进行推送。当用户想要提交或推送代码时使用。
---

## 💡 初学者指南 (Beginner's Guide)

此 Skill 是你的 Git 助手。它会自动分析你的代码变更，写出规范的 Commit Message（例如 `feat: 增加登录功能`），并且在推送时自动检测本地代理（Clash/v2ray），解决 GitHub 连接不稳定的问题。

- **功能**:
    1. **智能提交**: 自动写 Commit Message，不需要你想破头。
    2. **智能推送**: 自动检测 7890/7897 等代理端口，确保推送成功。
- **使用方法**: 只需说 "提交代码" 或 "Push"。

---

# Git Smart Workflow (Commit & Push)

This skill handles the full Git workflow: Staging -> Committing -> Pushing.
It is specifically optimized for network environments in China (e.g., using Clash).

## Steps

### 1. Smart Commit (提交)

1.  **Check Staged**: Run `git --no-pager diff --cached --name-only`.
    - If empty, ask user to stage files first (or offer to stage all).
2.  **Generate Message**: Analyze `git --no-pager diff --cached` and generate a Conventional Commit message in **Chinese**.
    - Format: `<type>(<scope>): <subject>`
3.  **Confirm & Commit**: Show message and ask to commit.
    - Command: `git commit -m "<message>"`

### 2. Smart Push (推送)

Ask user: "Should I push the changes?"

If **Yes**:

1.  **Self-Healing Check (Dependency Installation)**:
    - Check if `scripts/git-smart-push.ps1` exists.
    - **If missing**: Automatically create it using the `Write` tool with the following content (do not ask user, just do it):
      ```powershell
      # scripts/git-smart-push.ps1
      # Git Smart Push for China Network Environment
      param([string[]]$GitArgs); $ErrorActionPreference = "Stop"
      function Write-Color { param([string]$Message, [ConsoleColor]$Color = "White"); Write-Host $Message -ForegroundColor $Color }
      Write-Color "`n🚀 Trae Smart Push: Initializing Network Optimization..." "Cyan"
      $CandidatePorts = @(7890, 7897, 10808, 1080, 51837); $ActiveProxy = $null
      Write-Color "🔍 Scanning for local proxy (Clash/v2ray)..." "Gray"
      foreach ($Port in $CandidatePorts) { if (Test-NetConnection -ComputerName 127.0.0.1 -Port $Port -InformationLevel Quiet -WarningAction SilentlyContinue) { $ActiveProxy = "http://127.0.0.1:$Port"; Write-Color "✅ Found active proxy on port: $Port" "Green"; break } }
      $GitCommand = "git"; $GitParams = @(); if ($ActiveProxy) { Write-Color "🌐 Using Proxy: $ActiveProxy" "Green"; $GitParams += "-c", "http.proxy=$ActiveProxy", "-c", "https.proxy=$ActiveProxy" } else { Write-Color "⚠️  No local proxy detected. Attempting Direct Connection..." "Yellow" }
      $GitParams += "push"; if ($GitArgs) { $GitParams += $GitArgs }; $CmdString = "$GitCommand " + ($GitParams -join " "); Write-Color "exec: $CmdString" "DarkGray"
      try { & $GitCommand $GitParams; if ($LASTEXITCODE -eq 0) { Write-Color "`n✨ Push Successful!" "Green"; exit 0 } else { throw "Git push exited with code $LASTEXITCODE" } } catch { Write-Color "`n❌ Push Failed." "Red"; exit 1 }
      ```

2.  **Execute Smart Push**:
    - Run: `powershell -ExecutionPolicy Bypass -File scripts/git-smart-push.ps1`
    - (The script handles proxy detection and injection automatically).

## Usage Example

User: "提交代码"
Agent:

1. Generates "feat(sidebar): 增加折叠功能"
2. Commits.
3. Asks "Push?" -> User "Yes"
4. Tries direct push -> Fails -> Tries proxy push (7890) -> Success!
