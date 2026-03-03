# 1. macOS / Windows 环境准备

你需要的最小环境（建议一次装齐）：

- VS Code（编辑器 + 插件入口）
- Git（看 diff、回滚、提交）
- Node.js（Claude/Gemini/Codex 三套 CLI 都用得到；建议 Node 20+，更推荐 Node 22）
- Windows 用户：优先原生安装 Node.js；遇到兼容问题再上 WSL2（可选）

## 1.1 macOS ：先装 Homebrew（推荐）

Homebrew 是 macOS 上最省事的软件安装方式（装 Node/Git/各类 CLI 都靠它）

安装 Homebrew（官方命令以 Homebrew 网站为准；安装后按提示把 brew 加到 PATH）：

- [Homebrew](https://brew.sh/)

常用安装（示例）：

```bash
brew install git node
brew install --cask visual-studio-code
brew install gemini-cli
brew install --cask claude-code codex
```

验证：

```bash
git --version
node -v
npm -v
code --version
claude --version
gemini --version
codex --version
```

## 1.2 Windows：优先原生安装（推荐）

Windows 上跑 vibecoding，最省事的路径通常是：先把 Node/Git/VS Code 装好，直接在 PowerShell/Windows Terminal 里跑 CLI + 用 VS Code 插件。

关于 winget（可选）：

- winget 是 Windows 的包管理器，优点是"一行命令安装/更新"；缺点是部分电脑可能没装或被公司策略禁用
- 如果你要用 winget，建议先检查是否可用：

```powershell
winget --version
```

如果不可用：直接用官网安装包即可（或者安装/更新 Microsoft Store 的 "App Installer" 后再试）。

1. 安装 Node.js
- 官网安装包（LTS）：[https://nodejs.org/](https://nodejs.org/)
- 或 winget：

```powershell
winget install OpenJS.NodeJS.LTS
```

2. 安装 Git
- 官网安装包（Git for Windows）：[https://git-scm.com/download/win](https://git-scm.com/download/win)
- 或 winget：

```powershell
winget install Git.Git
```

3. 安装 VS Code
- 官网：[https://code.visualstudio.com/](https://code.visualstudio.com/)
- 或 winget：

```powershell
winget install Microsoft.VisualStudioCode
```

4. 验证（重开一个新终端再运行）

```powershell
node -v
npm -v
git --version
```

5. （推荐）Windows 上安装三套 CLI 都用 npm（后面各工具章节也会再写一遍）

```powershell
npm install -g @anthropic-ai/claude-code
npm install -g @google/gemini-cli
npm install -g @openai/codex
```

### 1.2.1 Windows（可选）： WSL2 + Ubuntu（遇到兼容问题再用）

什么时候建议上 WSL2：
- 你遇到了 Windows 终端下的兼容问题（尤其是某些 MCP / stdio / shell 命令）
- 你更想要接近 macOS/Linux 的命令行体验

安装 WSL2（PowerShell 管理员）：

```powershell
wsl --install -d Ubuntu
```

进入 Ubuntu 后安装基础依赖：

```bash
sudo apt update
sudo apt install -y git curl build-essential
```

在 Ubuntu 里装 Node（推荐 nvm）：

```bash
curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install 22
nvm use 22
```

VS Code + WSL（推荐）：
1. Windows 安装 VS Code
2. VS Code 扩展安装：Remote - WSL
3. 在 Ubuntu 里进入项目目录运行：

```bash
code .
```

性能提示：项目尽量放在 Linux 文件系统里（如 `~/code/myproj`），不要放在 `/mnt/c/...` 里跑大量编译/测试。

## 1.3 环境变量 / .env 放哪里

原则：
- API Key 永远不要写进仓库（把 `.env`、`*.key` 加进 `.gitignore`）
- Windows 的环境变量（`setx`）不会自动同步到 WSL；WSL 里要单独 `export`

macOS（zsh）把 key 写进 `~/.zshrc`（示例）：

```bash
cat >> ~/.zshrc <<'EOF'
export GEMINI_API_KEY="YOUR_KEY"
export OPENAI_API_KEY="YOUR_KEY"
export ANTHROPIC_API_KEY="YOUR_KEY"
EOF
source ~/.zshrc
```

WSL（bash）把 key 写进 `~/.bashrc`（示例）：

```bash
cat >> ~/.bashrc <<'EOF'
export GEMINI_API_KEY="YOUR_KEY"
export OPENAI_API_KEY="YOUR_KEY"
export ANTHROPIC_API_KEY="YOUR_KEY"
EOF
source ~/.bashrc
```

Windows（PowerShell）设置到系统环境变量（示例；重开终端生效）：

```powershell
setx GEMINI_API_KEY "YOUR_KEY"
setx OPENAI_API_KEY "YOUR_KEY"
setx ANTHROPIC_API_KEY "YOUR_KEY"
```
