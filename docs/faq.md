# 4. 常见问题（FAQ）

## 4.1 claude/gemini/codex 命令找不到

常见原因：
- Node/npm 没装好，或版本太旧
- npm 全局 bin 不在 PATH

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
- 先把 Node 升到 20+（更推荐 22）
- 重开终端 / 重启 VS Code

## 4.2 浏览器登录打不开（WSL/远程环境）

Codex：用 device flow（官方支持）：

```bash
codex login --device-auth
```

Claude/Gemini：
- 多数情况下会给你一个 URL，让你复制到有 GUI 的浏览器完成登录

## 4.3 在 Windows 里 setx 了 key，但 WSL 里读取不到

原因：Windows 环境变量不会自动同步到 WSL。

解决：在 WSL 的 `~/.bashrc` / `~/.zshrc` 里再 `export` 一遍（见 1.3）。

## 4.4 Gemini 的 .env 不生效 / 读不到配置

优先检查：
- 该目录是否被标记为 Trusted Folder（可信目录）
- key 是否写在 `~/.gemini/.env` 或正确的环境变量里（`GEMINI_API_KEY`）

## 4.5 npm install -g ... 报权限错误（EACCES）

不用 `sudo npm -g` 也可以用

- macOS：用 Homebrew 安装 Node
- WSL：用 nvm 安装 Node

## 4.6 MCP 启动失败 / Connection closed

高频原因：
- Node 版本过低导致 `npx`/依赖不兼容
- Windows 原生环境对 stdio/命令解析更敏感（遇到这类问题再用 WSL2）
- MCP server 需要的外部命令没装（比如 `uvx` 需要 `uv`）

排查方式：
1. 先在终端单独跑一遍 MCP 命令（例如 `npx ...`）看具体报错
2. 再回到 CLI/插件里启用 MCP

## 4.7 CC-switch 切换后没生效

按这个顺序排查：
1. 确认 CC-switch 当前选中的 Provider 是你要的
2. 按 CC-switch 文档提示重启对应客户端（Claude/Codex 需要重启；Gemini 通常不需要）
3. 去对应配置文件路径确认是否被写入（CC-switch 文档里给了默认路径）
