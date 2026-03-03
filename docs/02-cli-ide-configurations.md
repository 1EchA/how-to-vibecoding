# 2. Claude / Codex / Gemini：安装与基础配置（官方 vs CC-switch）

## 2.0 CC-switch（中转用户推荐；直连官方可跳过）

CC-switch 主要帮你做三件事：
- 统一管理不同「中转商/网关」的 Base URL / Key，并一键切换
- 统一管理/同步 MCP、Agents、Skills（适合多工具链并行）
- 自动写入各家 CLI 的配置文件位置（不用你手动找路径）

安装：

- macOS（Homebrew）：

```bash
brew tap farion1231/ccswitch
brew install --cask cc-switch
```

- Windows：到 [Releases](https://github.com/farion1231/cc-switch/releases) 下载 `.msi` 或 portable 版本安装

切换生效提示：
- Claude Code：切换后需要重启 Claude Code
- Codex：切换后需要重启 Codex CLI 和 IDE 插件
- Gemini CLI：切换后无需重启（每次切换会重写 `~/.gemini/.env`）

恢复官方登录：
- Claude：选择「官方登录」预设，重启 Claude Code，按官方登录流程操作
- Gemini：选择「Google Official」预设，按官方登录流程操作

下面进入三套工具的「官方配置」与「CC-switch 中转配置」。

---

## 2.1 Claude：Claude Code（ CLI + VS Code）

### 2.1.1 安装

macOS / Linux / WSL（官方安装脚本）：

```bash
# 全局安装 Claude Code
npm install -g @anthropic-ai/claude-code
claude --version

# 有权限问题就用sudo
sudo npm install -g @anthropic-ai/claude-code
```

Windows（推荐：npm，最省事）：

```powershell
npm install -g @anthropic-ai/claude-code
claude --version
```

Windows（备选：官方安装脚本 / winget）：

```powershell
irm https://claude.ai/install.ps1 | iex
claude --version
# 或
winget install Anthropic.ClaudeCode
```

### 2.1.2 官方配置：账号登录 / API Key

方式 A：官方账号登录

```bash
claude
```

按提示在浏览器完成登录即可。

方式 B：API Key（脚本化/团队常用）

- 设置环境变量（二选一都行）：
  - `ANTHROPIC_API_KEY`
  - `ANTHROPIC_AUTH_TOKEN`

macOS/WSL 示例：

```bash
export ANTHROPIC_API_KEY="YOUR_KEY"
claude
```

（可选）如果你们公司/中转商提供的是「网关地址」，Claude Code 也支持配置 LLM gateway：
- `ANTHROPIC_BASE_URL`
- `ANTHROPIC_API_KEY` / `ANTHROPIC_AUTH_TOKEN`

### 2.1.3 CC-switch 中转配置（推荐）

CC-switch 会把 Claude 的配置写到这些位置：
- `~/.claude/settings.json`（写入环境变量等）
- `~/.claude.json`（写入 MCP servers）

建议流程（最省事）：
1. 在 CC-switch 里新增/选择你的中转商 Provider（通常就是 Base URL + Key）
2. 选择 Claude 对应的 Provider 并应用
3. 重启 Claude Code，再运行：

```bash
claude --version
claude
```

### 2.1.4 VS Code 插件

- VS Code 扩展市场安装：Claude Code（按提示登录/连接）
- Claude Code 常用：在项目里输入 `/init` 生成 `CLAUDE.md`（把项目规则写清楚）

---

## 2.2 Gemini：Gemini CLI + Gemini Code Assist（VS Code）

### 2.2.1 安装 Gemini CLI

macOS / Linux / WSL（npm）：

```bash
npm install -g @google/gemini-cli
gemini --version

# 有权限问题就用sudo安
sudo npm install -g @google/gemini-cli
```

Windows（推荐：npm / PowerShell）：

```powershell
npm install -g @google/gemini-cli
gemini --version
```

macOS（Homebrew）：

```bash
brew install gemini-cli
gemini --version
```

### 2.2.2 官方配置：账号登录 / API Key

方式 A：官方账号登录（Google 登录）

```bash
gemini
```

按交互提示选择 Google 登录即可。

方式 B：API Key

- 获取 API Key（Google AI Studio）：[https://aistudio.google.com/apikey](https://aistudio.google.com/apikey)
- 设置环境变量：

```bash
export GEMINI_API_KEY="YOUR_KEY"
gemini
```

Trusted Folder：
- Gemini CLI 有「可信目录」机制；不信任的目录里 `.env`、本地配置、部分工具能力会受限
- 遇到「明明配了 `.env` 但不生效」，先去检查是否已信任该目录

### 2.2.3 CC-switch 中转配置

CC-switch 会把 Gemini 的配置写到这些位置：
- `~/.gemini/.env`（写入 `GEMINI_API_KEY` 或 `GOOGLE_GEMINI_API_KEY` 等）
- `~/.gemini/settings.json`（写入基础设置、MCP servers）

这部分和claude在ccswitch的配置几乎相同
1. 在 CC-switch 里新增/选择你的中转商 Provider（通常就是 Base URL + Key）
2. 选择 Claude 对应的 Provider 并应用
3. 重启 Claude Code，再运行：

切换后重启 Gemini CLI；直接运行验证：

```bash
gemini --version
gemini
```

### 2.2.4 VS Code 插件：Gemini Code Assist

- VS Code 扩展市场安装：Gemini Code Assist
- 按提示用 Google 账号登录
- 如果你要用更自动化的 agent 能力：在插件里开启 Agent mode，并配好 Gemini CLI 的设置/MCP

---

## 2.3 OpenAI ：Codex CLI + Codex IDE （VS Code）

### 2.3.1 安装 Codex CLI

macOS / Linux / WSL：

```bash
npm i -g @openai/codex
codex --version

# 有权限问题就用sudo安
sudo npm i -g @openai/codex
```

Windows（推荐：npm / PowerShell）：

```powershell
npm i -g @openai/codex
codex --version
```

说明：Codex 在 Windows 原生环境可能仍属于 experimental；如果你遇到兼容问题，再切到 WSL2（见 1.2.1）。

### 2.3.2 官方配置：账号登录 / API Key

方式 A：官方账号登录（ChatGPT 登录）

```bash
codex login
```

方式 B：API Key
- 运行 `codex login`，在交互里选择 API key 并粘贴
- 或者设置环境变量：

```bash
export OPENAI_API_KEY="YOUR_KEY"
```

提示：Codex 会把登录信息缓存到 `~/.codex/auth.json`。

### 2.3.3 CC-switch 中转配置

CC-switch 会把 Codex 的配置写到这些位置：
- `~/.codex/auth.json`（写入 `OPENAI_API_KEY`）
- `~/.codex/config.toml`（写入 provider / 模型 / MCP servers 等）

依旧和claude的配置差不多：
1. 在 CC-switch 里新增/选择你的中转商 Provider（通常就是 Base URL + Key）
2. 选择 Claude 对应的 Provider 并应用
3. 重启 Claude Code，再运行：

切换后建议：
1. 重启 Codex CLI
2. 重启 VS Code（或 Reload Window）
3. 验证：

```bash
codex --version
codex
```

### 2.3.4 VS Code 插件：Codex IDE

- VS Code 扩展市场安装：Codex IDE
- Windows 用户建议在 WSL2 中使用（官方也提供 Windows/WSL 指南）
