# VibeCoding 入门教程 (How to VibeCoding)

[![Linux.do Popular](https://img.shields.io/badge/Linux.do-%E7%83%AD%E9%97%A8%E6%8E%A8%E8%8D%90-gold?style=for-the-badge&logo=linux&logoColor=white)](https://linux.do/t/topic/1615649)
[![Platform](https://img.shields.io/badge/Platform-macOS%20%7C%20Windows-blue?style=flat-square)](#)
[![Theme](https://img.shields.io/badge/Topic-AI%20Coding-brightgreen?style=flat-square)](#)

> VibeCoding 全面指南与实战教程：涵盖 macOS / Windows 环境搭建，Claude/Gemini/Codex CLI 与 VS Code 插件配置，以及 Agents 和 MCP 最佳实践。

---

*这里放置你在 Linux.do 或者实际使用中的亮眼截图*
![VibeCoding 演示截图放置处](./assets/screenshot-placeholder.png)

---

## 📖 快速导航 (Table of Contents)

这篇指南被拆分成几个结构化的部分，方便查阅：

- 💻 [01. macOS / Windows 环境准备](./docs/01-environment-setup.md) —— 你需要的最小环境及 Node.js 等基础配置。
- 🤖 [02. CLI 与 IDE 插件配置](./docs/02-cli-ide-configurations.md) —— Claude、Gemini、Codex 三大主流 AI 工具的实战配置，以及官方与 CC-switch 推荐方案。
- 🧠 [03. 核心概念：Agents 与 MCP](./docs/03-agents-and-mcp.md) —— 了解什么是 CLI / IDE 区别、Agents 定义、以及强大的 Model Context Protocol。
- ❓ [04. 常见问题排查与解答 (FAQ)](./docs/faq.md) —— 无法找到命令、WSL 环境变量配置等疑难杂症的解决。
- 📚 [05. 官方/高质量文档入口](./docs/05-references.md) —— Claude/Gemini/Codex 官方文档和相关资源汇总。

## 🚀 快速开始 (Quick Start)

### 1. 全局安装 Claude Code (推荐)
```bash
# 使用 npm 全局安装
npm install -g @anthropic-ai/claude-code

# 检查版本
claude --version

# 开始使用
claude
```

### 2. 初始化项目规则
在你的项目根目录下：
```bash
# 生成 CLAUDE.md 等 Agent 规则文件
/init
```

## 🛠 推荐工作流 (Recommended Workflow)

强烈推荐中转商用户使用 **[CC-switch](https://github.com/farion1231/cc-switch)** 来统一管理。它可以：
- 统一管理不同平台 Base URL / Key 并一键切换
- 统一分配和同步 MCP、Agents、Skills
- 自动写入命令行工具的配置文件

*详细配置请参阅 [CLI 与 IDE 插件配置](./docs/02-cli-ide-configurations.md) 章节。*

## 🤝 贡献指南 (Contributing)

欢迎提交 Pull Request 补充 Skills 配置分享，或完善针对特定系统的报错经验。让我们共同完善这份 VibeCoding 秘籍！
