# 3. 概念速通：CLI、IDE、Agents、MCP

## 3.1 CLI vs IDE 插件

- CLI（命令行）：适合批量改动、跑测试、看 `git diff`、写可复现命令；更工程化
- IDE 插件（VS Code）：适合选区重写、边聊边改、快速修小问题；学习成本更低

新手建议：先用 CLI 把「登录 + 读项目 + 小改 + diff + 测试」跑通，再上复杂插件/自动化。

## 3.2 Agents（ 智能体 ）是什么

你可以把 Agent 理解为：

"一个能读代码、会拆任务、能调用工具（写文件/跑命令/拉取资料）、并按你的规则迭代完成目标的 AI 工程师"。

Agent 最关键的不是模型，而是两点：
- 你给它的规则（项目说明、禁止项、验收标准）
- 你对改动的审核方式（`git diff` + 测试）

## 3.3 AGENTS.md / CLAUDE.md / GEMINI.md

这些文件的作用都类似：把"项目怎么跑 + 什么能改/不能改 + 怎么验收"写清楚，让 AI 少犯错。

最小模板（建议放项目根目录，跟着项目走）：

AGENTS.md（偏 Codex/通用）：

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

CLAUDE.md（偏 Claude Code）：

```markdown
# CLAUDE.md
## Project
- Tech stack:
- How to run tests:

## Collaboration rules
- Explain first, then edit.
- Prefer small, reviewable diffs.
```

GEMINI.md（偏 Gemini CLI）：

```markdown
# GEMINI.md
## Project context
- What this repo is:
- How to run:
- How to test:
```

## 3.4 MCP（Model Context Protocol）是什么

一句话：MCP 是一套开放协议，让 AI 客户端（Claude/Codex/Gemini 等）用统一方式连接外部工具/数据源（文件系统、浏览器、数据库、GitHub、Notion……）。

新手用 MCP 的顺序建议：
1. 先不接 MCP，把"读代码/改代码/跑测试/审 diff"练熟
2. 再只接一个最安全的（比如 filesystem）
3. 最后才接有写入/网络/数据库权限的（每接一个就做一次验收）

这里推荐大家按照这个学习配置一下

[http://bilibili.com/video/BV1ZJsBznEt3/?spm_id_from=333.337.search-card.all.click](http://bilibili.com/video/BV1ZJsBznEt3/?spm_id_from=333.337.search-card.all.click)

官方入口：
- MCP（Anthropic 文档）：[Model Context Protocol](https://docs.anthropic.com/en/docs/mcp)

## 3.5 Skills 是什么

Skills 可以理解为"可复用的任务模板/流程"，把经常做的事标准化（例如：系统化排查 bug、发版流程、写周报、做监控）。

如果你团队里多人共用一套规则：
- 把 Skill 当成"约定俗成的 SOP"
- 让 AI 在每次任务开始先加载对应 Skill，会稳定很多

Skills推荐：

[https://skillsmp.com/zh](https://skillsmp.com/zh)

## 3.6 新手推荐工作流

每次写需求，建议都带上这 4 句：
1. 先解释你要怎么做
2. 再改代码
3. 给我看 diff
4. 跑测试/构建

这段在l站上的帖子让我非常受用，也推荐给大家

> 我看很多人每天CC用的很不舒服每天喊CC降智，然后我又是一个Python哲学的信奉者(凡事有最佳实践)，总结一下用CC四个月的体验。基本上各路大佬都提过，我只是一个总结者。我只总结最重要的三点，只记住这三个最简单的点让你CC智力直线上升:
>
> 1、除非在 planmode，永远不要纠正CC。如果CC做错了，直接clear git reset -> 修改原始 prompt叫他不要犯这个错。不然你去纠正他的话，你就会收到典中典的"您说的对"。
>
> 2、永远不要使用/compact。你就当没有这个命令，如果一个需求执行到 context满了还没完成，把这个需求拆成两个。clear git reset只做第一个需求。
>
> 3、如果一个需求三轮对话还没完成，重新来，context 用到60%就差不多了。想清楚你要干什么，clear git reset重新编辑prompt。
>
> 所以使用cc的流程大体是这样:
>
> 1、如果是简单需求，一次性描述清楚，让CC在两轮对话内完成，如果它做错了。重新来(彻底重新开始，不要纠正它让他改错，不要!)，把避免这个错误放到原始 prompt后面。如此反复直到你在三轮对话内完成这个需求
>
> 2、如果是复杂需求，使用planmode，你可以在里面指出CC的错误，反复商量，让它修改plan。最后让他一次性完成。注意，要保证他在完成时还有10%左右的 context可用。如果context不够了，拆分你的需求，直到可以在一个context内完成。
>
> 一句话总结:CC开发团队说过的，错了不要改，直接重新来。没错，绝对是正解。
>
> 就这么简单。
>
> 说实话我在CC出bug的那一周之外，很少感觉CC降智。降智确实存在，但影响远不及使用方法错误导致的降智。很多人都说CC在用完60%的context之后能力会急剧下降，我的感受也是如此。我在88code后台看缓存命中的时候，经常看到一个session压缩了几次还在继续用的，这个时候cc的智商已经完全不在了，降智是必然的。

这段好像是大米树写的，可惜现在88code中转已经接近跑路了
