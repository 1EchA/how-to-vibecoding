<div data-theme-toc="true"></div>

### AI 编程环境搭建指南 (Claude / Gemini / OpenAI)

> 覆盖工具：Claude Code、Gemini CLI、Codex CLI 及其对应的 VS Code 插件
>
> 适用系统：macOS, Linux, Windows 

首先推荐使用中转商服务的用户都先下载 CC-switch，这是一个开源的项目，可以帮助你完成环境变量的配置，mcp、agents、skills 的管理，

https://github.com/farion1231/cc-switch/tree/main

在 releases 中选择适合自己的版本下载

https://github.com/farion1231/cc-switch/releases


*目录*
1. macOS / Windows 环境准备
2. Claude / Codex / Gemini：安装与基础配置
3. 概念速通：CLI、IDE、Agents、MCP
4. 常见问题（FAQ）

<a id="env-setup"></a>
## 1. macOS / Windows 环境准备

你需要的最小环境（建议一次装齐）：

* VS Code（编辑器 + 插件入口）

* Git（看 diff、回滚、提交）

* Node.js（Claude/Gemini/Codex 三套 CLI 都用得到；建议 Node 20+，更推荐 Node 22）

* Windows 用户：优先原生安装 Node.js；遇到兼容问题再上 WSL2（可选）

<a id="macos-brew"></a>
### 1.1 macOS ：先装 Homebrew（推荐）

Homebrew 是 macOS 上最省事的软件安装方式（装 Node/Git/各类 CLI 都靠它）

安装 Homebrew（官方命令以 Homebrew 网站为准；安装后按提示把 brew 加到 PATH）：

* [Homebrew](https://brew.sh/)

![](upload://ze97yx4rqNHqfspTVZbKQhh4LXa.png)

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

<a id="win-native"></a>

### 1.2 Windows：优先原生安装（推荐）

Windows 上跑 vibecoding，最省事的路径通常是：先把 Node/Git/VS Code 装好，直接在 PowerShell/Windows Terminal 里跑 CLI + 用 VS Code 插件。

关于 `winget`（可选）：

* `winget` 是 Windows 的包管理器，优点是“一行命令安装/更新”；缺点是部分电脑可能没装或被公司策略禁用
* 如果你要用 `winget`，建议先检查是否可用：

```powershell
winget --version
```

如果不可用：直接用官网安装包即可（或者安装/更新 Microsoft Store 的 “App Installer” 后再试）。

1. 安装 Node.js

* 官网安装包（LTS）：https://nodejs.org/
* 或 winget：

```powershell
winget install OpenJS.NodeJS.LTS
```

2. 安装 Git

* 官网安装包（Git for Windows）： https://git-scm.com/download/win
* 或 winget：

```powershell
winget install Git.Git
```

3. 安装 VS Code

* 官网：https://code.visualstudio.com/
* 或 winget：

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

<a id="win-wsl"></a>

#### 1.2.1 Windows（可选）： WSL2 + Ubuntu（遇到兼容问题再用）

什么时候建议上 WSL2：

* 你遇到了 Windows 终端下的兼容问题（尤其是某些 MCP / stdio / shell 命令）
* 你更想要接近 macOS/Linux 的命令行体验

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
curl -fsSL [https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh](https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh) | bash
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

<a id="env-vars"></a>

### 1.3 环境变量 / `.env` 放哪里

原则：

* API Key 永远不要写进仓库（把 `.env`、`*.key` 加进 `.gitignore`）
* Windows 的环境变量（`setx`）不会自动同步到 WSL；WSL 里要单独 `export`

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

---

<a id="install-config"></a>

## 2. Claude / Codex / Gemini：安装与基础配置（官方 vs CC-switch）

<a id="cc-switch-config"></a>

### 2.0 CC-switch（中转用户推荐；直连官方可跳过）

CC-switch 主要帮你做三件事：

* 统一管理不同「中转商/网关」的 Base URL / Key，并一键切换
* 统一管理/同步 MCP、Agents、Skills（适合多工具链并行）
* 自动写入各家 CLI 的配置文件位置（不用你手动找路径）

安装：

* macOS（Homebrew）：

```bash
brew tap farion1231/ccswitch
brew install --cask cc-switch
```

* Windows：到 releases 下载 `.msi` 或 portable 版本安装

[CC-switch Releases](https://github.com/farion1231/cc-switch/releases)

切换生效提示：

* Claude Code：切换后需要重启 Claude Code
* Codex：切换后需要重启 Codex CLI 和 IDE 插件
* Gemini CLI：切换后无需重启（每次切换会重写 `~/.gemini/.env`）

恢复官方登录：

* Claude：选择「官方登录」预设，重启 Claude Code，按官方登录流程操作
* Gemini：选择「Google Official」预设，按官方登录流程操作

下面进入三套工具的「官方配置」与「CC-switch 中转配置」。

<a id="claude-setup"></a>

### 2.1 Claude：Claude Code（ CLI + VS Code）

#### 2.1.1 安装

macOS / Linux / WSL（官方安装脚本）：

```bash
# 全局安装 Claude Code
npm install -g @anthropic-ai/claude-code
claude --version

#有权限问题就用sudo
sudo npm install -g @anthropic-ai/claude-code
```

Windows（推荐：npm，最省事）：

```powershell
npm install -g @anthropic-ai/claude-code
claude --version
```

Windows（备选：官方安装脚本 / winget）：

```powershell
irm [https://claude.ai/install.ps1](https://claude.ai/install.ps1) | iex
claude --version

# 或
winget install Anthropic.ClaudeCode
```

#### 2.1.2 官方配置：账号登录 / API Key

方式 A：官方账号登录

```bash
claude
```

按提示在浏览器完成登录即可。

方式 B：API Key（脚本化/团队常用）

* 设置环境变量（二选一都行）：
* `ANTHROPIC_API_KEY`
* `ANTHROPIC_AUTH_TOKEN`



macOS/WSL 示例：

```bash
export ANTHROPIC_API_KEY="YOUR_KEY"
claude
```

（可选）如果你们公司/中转商提供的是「网关地址」，Claude Code 也支持配置 LLM gateway：

* `ANTHROPIC_BASE_URL`
* `ANTHROPIC_API_KEY` / `ANTHROPIC_AUTH_TOKEN`

#### 2.1.3 CC-switch 中转配置（推荐）

CC-switch 会把 Claude 的配置写到这些位置：

* `~/.claude/settings.json`（写入环境变量等）
* `~/.claude.json`（写入 MCP servers）

建议流程（最省事）：

1. 在 CC-switch 里新增/选择你的中转商 Provider（通常就是 Base URL + Key）
2. 选择 Claude 对应的 Provider 并应用
3. 重启 Claude Code，再运行：

```bash
claude --version
claude
```

#### 2.1.4 VS Code 插件

* VS Code 扩展市场安装：Claude Code（按提示登录/连接）
* Claude Code 常用：在项目里输入 `/init` 生成 `CLAUDE.md`（把项目规则写清楚）

<a id="gemini-setup"></a>

### 2.2 Gemini：Gemini CLI + Gemini Code Assist（VS Code）

#### 2.2.1 安装 Gemini CLI

macOS / Linux / WSL（npm）：

```bash
npm install -g @google/gemini-cli
gemini --version
#有权限问题就用sudo安
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

#### 2.2.2 官方配置：账号登录 / API Key

方式 A：官方账号登录（Google 登录）

```bash
gemini
```

按交互提示选择 Google 登录即可。

方式 B：API Key

* 获取 API Key（Google AI Studio）：https://aistudio.google.com/apikey
* 设置环境变量：

```bash
export GEMINI_API_KEY="YOUR_KEY"
gemini
```

Trusted Folder：

* Gemini CLI 有「可信目录」机制；不信任的目录里 `.env`、本地配置、部分工具能力会受限
* 遇到「明明配了 `.env` 但不生效」，先去检查是否已信任该目录

#### 2.2.3 CC-switch 中转配置

CC-switch 会把 Gemini 的配置写到这些位置：

* `~/.gemini/.env`（写入 `GEMINI_API_KEY` 或 `GOOGLE_GEMINI_API_KEY` 等）
* `~/.gemini/settings.json`（写入基础设置、MCP servers）

这部分和claude在ccswitch的配置几乎相同

1. 在 CC-switch 里新增/选择你的中转商 Provider（通常就是 Base URL + Key）
2. 选择 Claude 对应的 Provider 并应用
3. 重启 Claude Code，再运行：

切换后重启 Gemini CLI；直接运行验证：

```bash
gemini --version
gemini
```

#### 2.2.4 VS Code 插件：Gemini Code Assist

* VS Code 扩展市场安装：Gemini Code Assist
* 按提示用 Google 账号登录
* 如果你要用更自动化的 agent 能力：在插件里开启 Agent mode，并配好 Gemini CLI 的设置/MCP

<a id="codex-setup"></a>

### 2.3 OpenAI ：Codex CLI + Codex IDE （VS Code）

#### 2.3.1 安装 Codex CLI

macOS / Linux / WSL：

```bash
npm i -g @openai/codex
codex --version
#有权限问题就用sudo安
sudo npm i -g @openai/codex
```

Windows（推荐：npm / PowerShell）：

```powershell
npm i -g @openai/codex
codex --version
```

说明：Codex 在 Windows 原生环境可能仍属于 experimental；如果你遇到兼容问题，再切到 WSL2（见 1.2.1）。

#### 2.3.2 官方配置：账号登录 / API Key

方式 A：官方账号登录（ChatGPT 登录）

```bash
codex login
```

方式 B：API Key

* 运行 `codex login`，在交互里选择 API key 并粘贴
* 或者设置环境变量：

```bash
export OPENAI_API_KEY="YOUR_KEY"
```

提示：Codex 会把登录信息缓存到 `~/.codex/auth.json`。

#### 2.3.3 CC-switch 中转配置

CC-switch 会把 Codex 的配置写到这些位置：

* `~/.codex/auth.json`（写入 `OPENAI_API_KEY`）
* `~/.codex/config.toml`（写入 provider / 模型 / MCP servers 等）

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

#### 2.3.4 VS Code 插件：Codex IDE

* VS Code 扩展市场安装：Codex IDE
* Windows 用户建议在 WSL2 中使用（官方也提供 Windows/WSL 指南）

---

<a id="concepts"></a>

### 3. CLI、IDE、Agents、MCP、Skills、工作流

<a id="cli-vs-ide"></a>

### 3.1 CLI vs IDE 插件

* CLI（命令行）：适合批量改动、跑测试、看 `git diff`、写可复现命令；更工程化
* IDE 插件（VS Code）：适合选区重写、边聊边改、快速修小问题；学习成本更低

新手建议：先用 CLI 把「登录 + 读项目 + 小改 + diff + 测试」跑通，再上复杂插件/自动化。

<a id="what-is-agent"></a>

### 3.2 Agents（ 智能体 ）是什么

你可以把 Agent 理解为：

“一个能读代码、会拆任务、能调用工具（写文件/跑命令/拉取资料）、并按你的规则迭代完成目标的 AI 工程师”。

Agent 最关键的不是模型，而是两点：

* 你给它的规则（项目说明、禁止项、验收标准）
* 你对改动的审核方式（`git diff` + 测试）

<a id="config-files"></a>

### 3.3 AGENTS.md / CLAUDE.md / GEMINI.md

这些文件的作用都类似：把“项目怎么跑 + 什么能改/不能改 + 怎么验收”写清楚，让 AI 少犯错。

最小模板（建议放项目根目录，跟着项目走）：

`AGENTS.md`（偏 Codex/通用）：

```markdown
# AGENTS.md

## How to run
- Install: (fill me)
- Test: (fill me)

## Rules
- Make the smallest change that fixes the issue.
- Always show a plan before editing.
- Before finishing: run tests (or explain why you cannot).
- Never commit secrets (.env, keys) or credentials.

```

`CLAUDE.md`（偏 Claude Code）：

```markdown
# CLAUDE.md

## Project
- Tech stack:
- How to run tests:

## Collaboration rules
- Explain first, then edit.
- Prefer small, reviewable diffs.

```

`GEMINI.md`（偏 Gemini CLI）：

```markdown
# GEMINI.md

## Project context
- What this repo is:
- How to run:
- How to test:

```

<a id="what-is-mcp"></a>

### 3.4 MCP（Model Context Protocol）是什么

一句话：MCP 是一套开放协议，让 AI 客户端（Claude/Codex/Gemini 等）用统一方式连接外部工具/数据源（文件系统、浏览器、数据库、GitHub、Notion……）。

新手用 MCP 的顺序建议：

1. 先不接 MCP，把“读代码/改代码/跑测试/审 diff”练熟
2. 再只接一个最安全的（比如 filesystem）
3. 最后才接有写入/网络/数据库权限的（每接一个就做一次验收）

这里推荐大家按照这个学习配置一下

http://bilibili.com/video/BV1ZJsBznEt3/?spm_id_from=333.337.search-card.all.click

官方入口：

* MCP（Anthropic 文档）：[Model Context Protocol](https://docs.anthropic.com/en/docs/mcp)

<a id="what-is-skills"></a>

### 3.5 Skills 是什么

Skills 可以理解为“可复用的任务模板/流程”，把经常做的事标准化（例如：系统化排查 bug、发版流程、写周报、做监控）。

如果你团队里多人共用一套规则：

* 把 Skill 当成“约定俗成的 SOP”
* 让 AI 在每次任务开始先加载对应 Skill，会稳定很多

Skills推荐：

https://skillsmp.com/zh

<a id="workflow"></a>

### 3.6 新手推荐工作流

每次写需求，建议都带上这 4 句：

1. 先解释你要怎么做
2. 再改代码
3. 给我看 diff
4. 跑测试/构建

这段在l站上的帖子让我非常受用，也推荐给大家

> 我看很多人每天CC用的很不舒服每天喊CC降智，然后我又是一个Python哲学的信奉者(凡事有最佳实践)，总结一下用CC四个月的体验。基本上各路大佬都提过，我只是一个总结者。我只总结最重要的三点，只记住这三个最简单的点让你CC智力直线上升:
> 1、除非在 planmode，永远不要纠正CC。如果CC做错了，直接clear git reset ->修改原始 prompt叫他不要犯这个错。不然你去纠正他的话，你就会收到典中典的"您说的对"。
> 2、永远不要使用/compact。你就当没有这个命令，如果一个需求执行到 context满了还没完成，把这个需求拆成两个。clear gitreset只做第一个需求。
> 3、如果一个需求三轮对话还没完成，重新来，context 用到60%就差不多了。想清楚你要干什么，clear git reset重新编辑prompt。
> 所以使用cc的流程大体是这样:
> 1、如果是简单需求，一次性描述清楚，让CC在两轮对话内完成，如果它做错了。重新来(彻底重新开始，不要纠正它让他改错，不要!)，把避免这个错误放到原始 prompt后面。如此反复直到你在三轮对话内完成这个需求
> 2、如果是复杂需求，使用planmode，你可以在里面指出CC的错误，反复商量，让它修改plan。最后让他一次性完成。注意，要保证他在完成时还有10%左右的 context可用。如果context不够了，拆分你的需求，直到可以在一个context内完成。一句话总结:CC开发团队说过的，错了不要改，直接重新来。没错，绝对是正解。
> 就这么简单。
> 说实话我在CC出bug的那一周之外，很少感觉CC降智。降智确实存在，但影响远不及使用方法错误导致的降智。很多人都说CC在用完60%的context之后能力会急剧下降，我的感受也是如此。我在88code后台看缓存命中的时候，经常看到一个session压缩了几次还在继续用的，这个时候cc的智商已经完全不在了，降智是必然的。

这段好像是大米树写的，可惜现在88code中转已经接近跑路了

---

<a id="faq"></a>

## 4. 常见问题（FAQ）

<a id="faq-command"></a>

### 4.1 `claude/gemini/codex` 命令找不到

常见原因：

* Node/npm 没装好，或版本太旧
* npm 全局 bin 不在 PATH

排查（macOS/WSL）：

```bash
which node npm
node -v
npm -v
which claude gemini codex

```

排查（Windows PowerShell）：

```powershell
where node
node -v
npm -v
where claude
where gemini
where codex

```

解决思路：

* 先把 Node 升到 20+（更推荐 22）
* 重开终端 / 重启 VS Code

<a id="faq-login"></a>

### 4.2 浏览器登录打不开（WSL/远程环境）

Codex：用 device flow（官方支持）：

```bash
codex login --device-auth

```

Claude/Gemini：

* 多数情况下会给你一个 URL，让你复制到有 GUI 的浏览器完成登录

<a id="faq-env"></a>

### 4.3 在 Windows 里 `setx` 了 key，但 WSL 里读取不到

原因：Windows 环境变量不会自动同步到 WSL。

解决：在 WSL 的 `~/.bashrc` / `~/.zshrc` 里再 `export` 一遍（见 1.3）。

### 4.4 Gemini 的 `.env` 不生效 / 读不到配置

优先检查：

* 该目录是否被标记为 Trusted Folder（可信目录）
* key 是否写在 `~/.gemini/.env` 或正确的环境变量里（`GEMINI_API_KEY`）

### 4.5 `npm install -g ...` 报权限错误（EACCES）

不用 `sudo npm -g` 
也可以用

* macOS：用 Homebrew 安装 Node
* WSL：用 nvm 安装 Node

<a id="faq-mcp"></a>

### 4.6 MCP 启动失败 / `Connection closed`

高频原因：

* Node 版本过低导致 `npx`/依赖不兼容
* Windows 原生环境对 stdio/命令解析更敏感（遇到这类问题再用 WSL2）
* MCP server 需要的外部命令没装（比如 `uvx` 需要 `uv`）

排查方式：

1. 先在终端单独跑一遍 MCP 命令（例如 `npx ...`）看具体报错
2. 再回到 CLI/插件里启用 MCP

<a id="faq-ccswitch"></a>

### 4.7 CC-switch 切换后没生效

按这个顺序排查：

1. 确认 CC-switch 当前选中的 Provider 是你要的
2. 按 CC-switch 文档提示重启对应客户端（Claude/Codex 需要重启；Gemini 通常不需要）
3. 去对应配置文件路径确认是否被写入（CC-switch 文档里给了默认路径）

---

<a id="official-docs"></a>

## 官方/高质量文档入口

* CC-switch：[GitHub](https://github.com/farion1231/cc-switch/tree/main) / [Releases](https://github.com/farion1231/cc-switch/releases)
* Claude Code（Anthropic）
* Quickstart： https://docs.anthropic.com/en/docs/claude-code/quickstart
* Settings： https://docs.anthropic.com/en/docs/claude-code/settings
* Authentication： https://docs.anthropic.com/en/docs/claude-code/authentication
* LLM gateway（Base URL/Proxy）： https://docs.anthropic.com/en/docs/claude-code/llm-gateway
* MCP： https://docs.anthropic.com/en/docs/mcp


* Gemini（Google）
* Gemini CLI（GitHub）： https://github.com/google-gemini/gemini-cli
* Gemini Code Assist（VS Code）：https://cloud.google.com/gemini/docs/codeassist/set-up


* Codex（OpenAI）
* Codex（GitHub）： https://github.com/openai/codex
* Codex 文档（CLI/IDE/Auth/Config/Windows）： https://developers.openai.com/codex
* Authentication（含 device auth）： https://developers.openai.com/codex/auth
* Windows/WSL 指南： https://developers.openai.com/codex/windows


* WSL 安装（Microsoft）： https://learn.microsoft.com/windows/wsl/install

如果本篇教程对您有帮助的话，可以为我的github点个follow，后续我也会继续努力产出能够服务于大家的文档和产品的。

https://github.com/1EchA

```
