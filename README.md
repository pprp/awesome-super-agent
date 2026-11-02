# Awesome Super Agent [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

<p align="center"><img src="assets/cover.png" alt="Awesome Super Agent" width="100%"></p>

> 想了解 Super-agent，从这里开始。这是 DeepSeek Harness（DSH）研究原型的精选链接：它是什么、何时动手、怎么装，以及设计文章和代码差在哪。
>
> *English:* a curated list of links for the dsh-superagent research prototype, a proactive coding agent that runs on DeepSeek Harness. It is a research prototype, not a DeepSeek product. Entries are in Chinese; most linked official docs are in English.

Super-agent 在这里指官方仓库里的 dsh-superagent-prototype。它跑在 DeepSeek Harness 上，盯住**一个** github.com 仓库（可选再接飞书），只接允许名单里的人派的活。每张工单在独立 Session 和目录里做完，再回到原来的 Issue 或对话里交付，通常是一个 Pull Request。面板只把需要人处理的事摆出来。原则是尽可能少占用人的注意力：只在被点名时行动，或在团队写成规则、并且接受模式允许时行动。

公开仓库写明：这是研究原型，不是 DeepSeek 正式产品。发布前改写过，所以和内部版本、以及设计文章里的实现都不是同一份代码。文章里的一部分设计是未来方向，原型里没有。

两件容易看错的事。默认 `observe` 仍会做名单里的人点名的请求；没被点名的事只会记成「本来会接」。改已有 Issue 的正文，或在正文里补一个 @，收不到。

标记只用这六个：📘 官方 · 🟢 原型里有 · ⚪ 只在设计文章里 · 📝 社区解读 · 🛠️ 自己动手装 · 🔌 周边插件。资料核对日期：2026-11-02。星数会过期，下面不写。

**先读**

- [README](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/README.md) - 📘 它是什么，以及它不是什么.
- [000 Philosophy](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/000-philosophy.md) - 📘 注意力原则：何时行动、何时开口，以及操作者要收窄名单、减少噪音、为它发出的话负责.
- [001 Domain model](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/001-domain.md) - 📘 概念表、Trigger → Eval → 执行的流向、被否掉的概念、中英对照.
- [002 Authorization](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/002-authorization.md) - 📘 谁能开工、能走多远。门禁和订阅扇出是两条路。`off` / `observe` / `auto`，默认 `observe` 不是暂停，点名请求照样会做.
- [003 Execution](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/003-execution.md) - 📘 一张工单怎么执行、交付、恢复。一工单一 Session、一目录。自动合并只是提示词.
- [006 Deployment](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/006-deployment.md) - 📘 账号、安装、启动、排错。给编程智能体的剧本是「官方」一节的 SETUP.md，密钥不要贴进对话.
- [Super-agent 设计探索：迈向更自主的 Agent 系统](https://mp.weixin.qq.com/s/zoS7GlReWPhfCjENGxXCdg) - ⚪ 中文原文。常驻 Router 只做路由，Worker 隔离执行。文末写明原型只实现了其中一条路径。署名 Yujia Qiao，DeepSeek Harness team.

## Contents

- [官方](#官方)
- [设计](#设计)
- [文档](#文档)
- [部署](#部署)
- [解读](#解读)
- [衍生](#衍生)
- [生态](#生态)
- [讨论](#讨论)

## 官方

### 原型

- [deepseek-ai/dsh-superagent-prototype](https://github.com/deepseek-ai/dsh-superagent-prototype) - 🟢 面向单个 GitHub 仓库的主动式编程智能体，DSH bundle。MIT，DeepSeek Harness Team，2026.
- [SETUP.md](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/SETUP.md) - 🛠️ 给智能体看的安装剧本：探测环境、逐个提问、一份计划，再通过 Management API 落配置.
- [LICENSE](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/LICENSE) - 📘 MIT，Copyright (c) 2026 DeepSeek Harness Team.

### Harness

原型跑在它上面。产品页不介绍 Super-agent。

- [DeepSeek Harness](https://www.deepseek.com/en/harness/) - 📘 产品页。全球公开预览，Cordis 上「万物皆插件」.
- [deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness) - 📘 Harness 本体，MIT。外部贡献走 Discussions 和带 `dsh-plugin` topic 的插件，不接受外部 PR。见 [CONTRIBUTING.md](https://github.com/deepseek-ai/deepseek-harness/blob/master/CONTRIBUTING.md) / [中文](https://github.com/deepseek-ai/deepseek-harness/blob/master/CONTRIBUTING.zh.md).
- [Harness 文档](https://deepseek-harness.github.io/deepseek-harness/) - 📘 文档站。[Quickstart](https://deepseek-harness.github.io/deepseek-harness/en/guide/quickstart)、[插件市场](https://deepseek-harness.github.io/deepseek-harness/en/guide/plugin-market).
- [`@deepseek-ai/dsh`](https://www.npmjs.com/package/@deepseek-ai/dsh) - 📘 CLI：profile、插件、配置。装哪个版本，以 006 安装块里钉的版本和原型 `package.json` 的 `engines.dsh` 为准.

## 设计

文章和仓库是 2026-10-31 晚上一起出来的。deepseek.com 和 arXiv 上没有对应正文。英文标题 *Exploring Super-agent Design: Towards a More Autonomous Agent System* 是中文长文的机器翻译，仓库 README 引用了这个英文名，但没有给出链接。原文在页首。

- [知乎转载副本](https://blog.eimoon.com/p/deepseek-super-agent-design/) - 📝 微信打不开时的全文副本，保留了「这不是现有功能说明」的声明.
- [英文机器翻译](https://threadnavigator.com/thread/2116857257245987236/) - 📝 第三方标注为 @DeepSeekHarness 帖子的机器翻译。不是官方英译。原帖 [x.com/DeepSeekHarness/status/2116857257245987236](https://x.com/DeepSeekHarness/status/2116857257245987236).

⚪ 有、🟢 没有的：Watchdog 会话、云端循环加边缘守护进程、BYOM、异步 ReAct 的 `start()` / `stop()`。仓库里有的是 Engine、Ticket、Eval，以及 Router / Worker 两套 preset。`docs/003` 会把前台 shell 在 5 秒后转成后台任务，这和文章里的云端守护进程不是一回事。文中的实验是合成负载，编码换成了计时器。数字见原文，不要当成真实仓库上的生产力结论。

## 文档

000–003 和 006 在页首。编号文档按顺序读，后一篇只用前面已经定义过的概念。概念定义在 001，不在本清单里展开。

- [004 Prompts](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/004-prompts.md) - 📘 智能体读什么。团队可改的三段、技能覆盖、组装顺序、提示词版本不可变.
- [005 Panel](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/005-panel.md) - 📘 侧栏面板：现在要不要我、系统是否健康、一张工单从 Trigger 到 Ticket 的历史.
- [007 Architecture](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/007-architecture.md) - 📘 概念到 Runtime、Engine、类和源文件的开发者地图.
- [008 Cookbook](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/008-cookbook.md) - 📘 用一个虚构的 webhook Source 演示怎么加一种来源.

### 提示词

提示词在 [`prompts/`](https://github.com/deepseek-ai/dsh-superagent-prototype/tree/main/prompts)。团队通常改这三份：

- [`team/communication.md`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/prompts/team/communication.md) - 🟢 何时开口.
- [`team/scope.md`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/prompts/team/scope.md) - 🟢 何时做到可评审的 PR，何时停成草稿.
- [`team/delivery.md`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/prompts/team/delivery.md) - 🟢 提交署名和自动合并.

### 技能

- [`dsh-superagent-github`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/skills/dsh-superagent-github/SKILL.md) - 🟢 用 `gh` 读 Issue、PR、评审和 CI.
- [`dsh-superagent-feishu`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/skills/dsh-superagent-feishu/SKILL.md) - 🟢 用机器人身份读飞书消息和文档.

## 部署

步骤在页首的 006，剧本在「官方」的 SETUP.md。只支持 macOS 和 Linux。Windows 移植在「衍生」。Node 24 上 `status` / `stop` 的偏差在「讨论」。

要专门的 GitHub 账号，GitHub App 不能代替，因为收件读的是该用户的通知。Worker 和 Router 以操作者的系统用户、`danger-full-access` 运行。第一次 `start` 以退出码 3 结束，是在等配置。退出码四档以 006 的表为准。

- [`examples/profile.cordis.patch.yml`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/examples/profile.cordis.patch.yml) - 🛠️ 单独的 `superagent` profile 示例，面板示例在 `127.0.0.1:3082`。不要装进日常 web profile；已有 profile 时不要重跑 `dsh superagent --from-default-profile`.
- [larksuite/cli](https://github.com/larksuite/cli) - 🛠️ 飞书可选，文档用 1.0.90 测过。[飞书 CLI 文档](https://open.feishu.cn/document/mcp_open_tools/feishu-cli-let-ai-actually-do-your-work-in-feishu).

## 解读

官方文档优先。下面这些用来对照「文章写了什么、仓库做了什么」。

### 文章和代码

- [dsh-superagent 深挖报告](https://www.bytenote.net/article/dsh-superagent-deep-dive-report) - 📝 把实验条件、默认 `observe`、以及「隔离是 Session 加目录，不是沙箱」写清楚了.
- [本机管理接口与批次授权](https://www.ctfiot.com/314686.html) - 📝 对照 `6337fc8` 的社区审计，不是上游公告。本机管理接口用部署文档里的请求头辨认面板，保存 GitHub 来源时会运行配置里的 CLI。同一次评估里，门禁没通过的触发仍写在 `triggerIds` 上，Mandate 记的是这批里最强的发送者。作者的 PoC 仓库尚未公开。这个接口只监听本机.
- [合成实验反算：云端那半还只在文章里](https://halfatoken.com/insights/deepseek-super-agent/) - 📝 对设计长文里的同一组实验做反算，并在树里没有找到 Watchdog、边缘守护进程或 BYOM。反算是作者自己的，不是 DeepSeek 的结论.
- [Ticket、Mandate、Executor](https://www.harelasaf.com/articles/deepseek-dsh-superagent-prototype-ticket-mandate-executor-design) - 📝 只读了 000–003 和 007。订阅线程上的跟进不授予新权力；Worker 持有那条消息时，主会话只能停止这项工作。作者没有跑代码，也没有读设计长文.
- [MarkTechPost](https://www.marktechpost.com/2026/11/01/deepseek-dsh-superagent-router-worker-coding-agent/) - 📝 英文新闻稿，正确地把 p95 标成合成工具模拟。它把异步 ReAct 和未实现的想法放在一起，读的时候用上面的 ⚪ / 🟢 对照.

### 短文

- [Piotr Sobolewski（中文）](https://sobolewski.pro/zh/blog/deepseek-superagent) / [English](https://sobolewski.pro/en/blog/deepseek-superagent) - 📝 `off` / `observe` / `auto` 的短地图，以及 `danger-full-access` 为什么需要专用系统账号.
- [DeepSeek Super Agent: Complete Guide](https://super-agent.io/deepseek-super-agent/) - 📝 独立站点，声明与官方无关。讲清了 GitHub App 不能替代智能体账号，`observe` 不是把点名请求也暂停。图是概念示意，不是一次真实部署.
- [From a GitHub Request to a Reviewable PR](https://pragmaticai.substack.com/p/deepseek-superagent-from-a-github) - 📝 两分钟：第一单只改 README 的 Quick Start，先不要合并。文中写明视频里的 diff 不是一次真实 DSH 运行的产物。配套视频：[YouTube](https://www.youtube.com/watch?v=7vYILOY6_nE).
- [掘金：源码拆解 + 本地部署](https://juejin.cn/post/7702737817416105999) - 📝 七牛的文章，把模型指到他们自己的网关。安装顺序可以作索引；@ 的写法和星数以官方文档和仓库为准.
- [DSH Superagent 中文源码剖析](https://github.com/yang0228/dsh-superagent-analysis) - 📝 把 Engine、Intake、Policy 和 Executor 钉到 `6337fc8` 的文件和行号。截图是上游组件加演示数据，作者写明没有跑过真实工单.
- [OpenFree 条目](https://openfree.tools/zh/dsh-superagent) - 📝 一屏 README 复述，不是部署指南.
- [部署笔记（中文）](https://blog.laozhang.ai/zh/posts/deepseek-super-agent-github) / [English](https://blog.laozhang.ai/en/posts/deepseek-super-agent-github) - 📝 API 经销商的博客，作者写明没有真的派完一单。退出码以 006 的表为准.

### 目录

这些是 DSH 插件目录，不是 superagent 的安装方式。

- [dsh.plus](https://dsh.plus/en/) - 📝 策展过的 DSH 插件目录，收录了这个原型的一句话简介。站内那个「装进 DSH」的插件是目录插件本身.
- [awesome-dsh-plugin](https://github.com/awesome-dsh-plugin/awesome-dsh-plugin) - 📝 DSH 插件精选，CC0。站点 [awesome-dsh-plugin.com](https://awesome-dsh-plugin.com/)。官方在 [Harness discussion #215](https://github.com/deepseek-ai/deepseek-harness/discussions/215) 指向过它.
- [billLiao/awesome-dsh-plugin](https://github.com/billLiao/awesome-dsh-plugin) - 📝 按 GitHub topic `dsh-plugin` 收来的大清单，含弱相关。浏览站：[billliao.github.io/awesome-dsh-plugin](https://billliao.github.io/awesome-dsh-plugin/)。要找 superagent，用上面那份策展清单，而不是这份主题抓取.

## 衍生

这些不是 `deepseek-ai` 名下的代码。

- [saltapp/dsh-superagent-prototype-cn](https://github.com/saltapp/dsh-superagent-prototype-cn) - 📝 社区中文文档.
- [buyou-claw/dsh-superagent-win](https://github.com/buyou-claw/dsh-superagent-win) - 🔌 Windows 移植。上游只在 POSIX 上启动；这一份把 `PATH` 和 Git 配置收到单条命令上。prepare 要用 PowerShell（`$env:DSH_SUPERAGENT_DIR`），不能用 `sh`。测试仍按 Linux 写.
- [JHOpenDev/dsh-superagent-inbox](https://github.com/JHOpenDev/dsh-superagent-inbox) - 🔌 给原型加的文件收件箱 Source：一个目录驱动它，不需要外部账号.
- [wiva-hq/superagent-imessage](https://github.com/wiva-hq/superagent-imessage) - 🔌 实验性的 iMessage Source。本机走 Messages，Linux 走 Photon Spectrum，私聊默认关闭。有 `tests/imessage.spec.ts`，README 没有公开的端到端运行记录.
- [MYCF711/dsh-session-superagent](https://github.com/MYCF711/dsh-session-superagent) - 🔌 会话级：盯一个 GitHub 仓库，有变化就唤醒会话。自述凭证不进模型.
- [vicfei/dsh-super-agent](https://github.com/vicfei/dsh-super-agent) - 🔌 独立重实现，给 Claude Code、Codex、opencode 用，不跑在 DSH 上。默认 `observe` 只打印 WOULD-ACCEPT。0.5.0 起，工人干净退出且有新提交时会推 `dsh/ticket-` 加编号的分支并开 PR，链接写进报告；推送或开 PR 失败只记在报告里，不把工单改回未完成。npm 包是 `@vicfei/dsh-super-agent`.
- [LofiSu/deepseek-super-agent](https://github.com/LofiSu/deepseek-super-agent) - 🔌 独立重实现，自带薄 Harness 和 `ds-agent`。Router / Worker、Trigger 和 Watchdog 不跑在 DSH 上，作者写明不是 DeepSeek 产品.
- [cjy851096/nano-dsh-superagent](https://github.com/cjy851096/nano-dsh-superagent) - 🔌 约 3400 行的独立骨架，用来对照设计长文里原型没有的 Watchdog、异步 `start()` / `stop()` 和 Inline & Spill。不跑在 DSH 上；文中的批处理压测是作者自己的.
- [HulnotHutu/dsh-superagent-qq](https://github.com/HulnotHutu/dsh-superagent-qq) - 🔌 上游树上的 QQ 群来源。配置过的群里，经 OneBot v11 进来的消息变成 Trigger，回复回到群里。代码在 `src/sources/qq.ts`，说明在 `docs/009-qq-source.md`，本地小工具是 `napq`。作者写明与腾讯无关，用第三方客户端连接 QQ 可能违反用户协议.
- [Check-mj/dsh-superagent-windows](https://github.com/Check-mj/dsh-superagent-windows) - 🔌 Windows 适配，基线 `6337fc8`，差量在 `WINDOWS-DELTA.md`。另有设计长文里的 Watchdog：独立 Session，默认定时 30 分钟看工单是否停滞，设为 0 则关闭（`src/app/watchdog.ts`）。Router 分片默认关；打开后同一条 Thread 固定进同一个 Router，空分片到第一次投递才创建（`src/app/shards.ts`）。桌面壳不引用上游源码.
- [kulebbb/dsh-superagent](https://github.com/kulebbb/dsh-superagent) - 🔌 个人桌面插件，钉在 DSH `0.2.1-rc.5`。侧边栏目前只显示骨架；领域核心和「只有我」的 Policy 已有测试，作者写明还没接到插件入口.
- [shibaeff/dsh-superagent-prototype](https://github.com/shibaeff/dsh-superagent-prototype) - 🔌 上游 MIT 分支，给 ChatGPT 订阅加了一条脚本化的 Codex 登录。`codex-install` 默认装 `@jcy2387/dsh-codex-provider`，作者在 DSH `0.2.1-rc.3` 上核对过能列出模型，没有打过模型调用。`codex-install --codex-package dsh-codex-auth@0.3.4-rc.2` 复用本机 Codex CLI 的 `auth.json`，该插件只接受 `0.2.1-rc.1` 和 `rc.2`；作者在 `rc.2` 上记了一次 headless 回答，面板里没有再登录。说明在 `docs/009-codex-auth.md`.
- [mycguo/tebra-superagent](https://github.com/mycguo/tebra-superagent) - 🔌 仓库已从 `mycguo/dsh-superagent-prototype` 改名。署名 Tebra 的分支，README 写明供内部评估，不是 DeepSeek 的仓库。一个 `superagent` profile 同时盯多个 GitHub 仓库，`scripts/prepare-github-ticket` 按仓库分缓存。本机面板可以加 Google 登录（`docs/009-google-login.md`），不更换模型供应商。可选的 PostgreSQL 写在 `docs/011-postgresql.md`：默认仍是 SQLite，要在 profile 的 `dsh-superagent` 项里显式选 `postgres`，只设环境变量不会切换；连不上就拒绝启动，不会退回 SQLite。同一 schema 用会话顾问锁，避免两个运行时同时派工。从 SQLite 导入默认只做只读检查，会话文件和 Git 工作区仍在原来的磁盘上.
- [alasong/ssa1101](https://github.com/alasong/ssa1101) - 🔌 独立的轻编排（目录名 `dsh-superagent-lite`）。`scripts/server.ts` 默认对每个 assignment spawn `dsh --json`，`SA_WORKER=fake` 才用假会话。README 仍写 SessionFactory 是桩，Cordis bundle 只打挂载日志，准入记账还在外部的 kaa.
- [waixv-superagent/dsh-superagent-prototype](https://github.com/waixv-superagent/dsh-superagent-prototype) - 🔌 上游分支。只读 TAPD 来源默认关闭、不回写；任务中心按租约把工单放进共享工作区，租约不是沙箱。飞书可以另选一个话题群：接下工单后在该群开一个话题，后续回复和卡片转到话题里，并在原来的会话留一条指向话题的回复；这个群必须收全部消息，因为话题里的回复不带 @，留空则仍回到原来的聊天。工单新建的飞书文档用请求者自己的身份创建和修改，不先用机器人建好再转让；没有点名的身份时会问请求者，而不是退回机器人。P1 方案卡在 `src/app/plan.ts`，可以带上 Worker 自己的问题、2 到 4 个选项和可选的说明；同一句回答若对上多张在等的方案，会保持含糊并追问是哪张工单。`docs/012` 的阶段表把这张方案卡写成已有，但「P1 未过门不许写代码」仍标成待实现。p4 预检里，目标是另一个仓库的工单要在 P1 报阻塞，并写上那个仓库和它需要的 Git 通道，而不是在当前工作区里改。恢复的 Assignment 开场会带上工单、阶段和上一份报告。更早的提交把交付改成可切换的 Perforce shelve / Swarm，默认仍是 Git，包装会拒绝 `p4 submit`。只有 p4 后端才跟流水线：宿主上的 `orchestrator_pipeline_check` 读 Swarm 的测试和 AI 结论，或失败的 Jenkins 日志，令牌留在执行器设置里，不进 Worker 的命令行；Git 后端在本机检查后就结束。已经合并的流水线在有人恢复工单时只留历史，面板不再把它当成还没入库。总览页在值班板和编辑器页之间切换。编辑器页只报告谁占着共享编辑器、近一小时各工单的时间线，以及当前工具是否用到编辑器，页上可以展开 Worker 最近的工具和 MCP 调用；它不启动也不停止。`docs/014` 写明面板今天只会观察，租约、心跳、排队和人插队仍是设计。README 开头仍是上游原文.
- [Yao-MY06/more-agent](https://github.com/Yao-MY06/more-agent) - 🔌 默认分支 `feat/m0-shadow-run` 把原型引擎钉在 `6337fc8`，放进 `vendor/dsh-superagent`，`src/core` 按清单哈希未改。M0 只影子运行：Leader 用 `claude -p` 选项目，ShadowExecutor 只记账，不启动 Worker，也不发消息。不跑在 DSH 上。MIT.
- [yingpengma/cc-super-agent](https://github.com/yingpengma/cc-super-agent) - 🔌 按设计长文做的独立 Router / Worker，给 Claude Code 用，不跑在 DSH 上，也不是 `6337fc8` 的分支。Watchdog 是作者自己的 cron，每 15 分钟一次。README 里的对话是示意，不是一次真实运行.
- [xyzOvO/dsh-superagent-prototype](https://github.com/xyzOvO/dsh-superagent-prototype) - 🔌 默认分支 `fix/windows-support`。win32 上插件有六处不改就起不来：盘符绝对路径、子进程直接带环境、prepare 走 `cmd.exe`、编排会话白名单用 `pwsh`（否则 `tools.restrict` 让整个插件加载失败）、另写 `gh.cmd` 并在外来文件扫描里跳过 `*.cmd`。作者在提交说明里记了 Windows 11、Node 24 上从 Issue 到 PR 的一次往返。服务脚本仍依赖 `fcntl`，Windows 上改走面板或 HTTP API.
- [0xdrmnhttn13/dsh-superagent-prototype](https://github.com/0xdrmnhttn13/dsh-superagent-prototype) - 🔌 独立的本地调查代理，README 称为 Superagent Investigator。SQLite 里仍是 Trigger、Policy、Mandate、Ticket 和 Assignment，执行换成受限的 Pi 会话，只注册 `sa_context`、`sa_evidence`、`sa_report`。本机仅所有者可连的 Unix socket 收 UTF-8 证据。不跑 DSH，也没有 GitHub 或飞书来源。工具限制在应用层，不是操作系统沙箱。MIT.
- [jwangkun/dsh-superagent](https://github.com/jwangkun/dsh-superagent) - 🔌 上游 `6337fc8` 的社区分支，不是 DeepSeek 的仓库。同一 GitHub 账号写下的 Issue 或评论，只有正文以 `@` 加上自己的登录名开头，才算这个账号自己的点名请求，其余自己写的回复仍跳过；通知流和按仓库轮询走同一条规则。`setup` 只在自检时改 `PATH` 和 `GIT_CONFIG_*`，查完就恢复，避免放进已有 profile 后留在进程里。默认 `stateDir` 是状态目录下的 `state/dsh-superagent`。`package.json` 为 0.1.3，GitHub Release 停在 v0.1.2，npm 上没有名为 `dsh-superagent` 的包。仓库带编译好的 `lib/`。MIT.
- [mitkox/copilot-superagent](https://github.com/mitkox/copilot-superagent) - 🔌 独立的 VS Code 扩展。工单引擎来自原型，执行换成 GitHub Copilot SDK `1.0.28` 的会话，每张工单一个 Git worktree。收件是 Copilot Chat、GitHub 和 Microsoft Teams。版本 0.4.0 删掉了旧的 DSH 运行时、飞书、网页面板和启动器，并要求 Copilot Business 或 Enterprise 账号加上 Copilot CLI `1.0.105`；自定义供应商和个人套餐的推理地址会被拒绝。扩展宿主关掉后服务就停，命令仍用操作者的系统权限。作者写明不是 GitHub、Microsoft 或 DeepSeek 的产品。MIT.
- [aicodeking/dsh-superagent-gui](https://github.com/aicodeking/dsh-superagent-gui) - 🔌 本机网页，指向任意文件夹。已安装的智能体命令行在单独的 Git worktree 或普通副本里干活，目录要等 Merge 或 Apply 才写回。保留 Trigger、Mandate 和默认 `observe`：没被点名的事变成建议，等人动手。故意不带飞书、GitHub 轮询和 cordis 打包。DSH 用 `dsh --profile headless`，作者标成未实测。Node 22.12+，没有运行时依赖，版本 0.1.0。macOS 和 Linux。不是 DeepSeek 的产品。MIT.
- [MingoYung/dsh-superagent-power](https://github.com/MingoYung/dsh-superagent-power) - 🔌 DSH 输入栏里的全局开关，管已经装好的独立 superagent profile，不重写工单引擎。开关属于 `DSH_HOME` 加这个 profile，不属于某个会话。关闭先调用 `Runtime.stop()`，再 `appExit`；确认进程退出后界面才显示已关闭，有活动任务时要再确认才会中断。子进程是 `node <cli> --profile superagent --no-open`。作者在 Windows、DSH NEXT `2.0.21-next`、内嵌 `0.2.1-rc.4`、独立 CLI `0.2.1-rc.5` 上验收过，macOS 和 Linux 还没有。版本 0.1.0，`engines.dsh` 钉在 `0.2.1-rc.4` 或 `rc.5`。不是 DeepSeek 的插件。MIT.
- [lesterppo/dsh-superagent-rs](https://github.com/lesterppo/dsh-superagent-rs) - 🔌 原型核心的 Rust 0.1.0 移植：领域模型、Policy 门禁和 Engine，Worker 在本地目录里跑，路径逃逸会拒绝。回复只记进 outbox，不发飞书或 GitHub，也没有面板、DSH Session 和 cordis 打包。不在 `auto` 时，推断接受记成 would-accept 再忽略。可以接 Muse Spark 或 DeepSeek 的聊天接口；作者写明 Muse 还没有密钥，缺密钥就退出，不假装调用成功。不跑在 DSH 上。MIT.
- [RichardoMrMu/mini-super-agent](https://github.com/RichardoMrMu/mini-super-agent) - 🔌 教学用的事件引擎，对照设计长文和 `6337fc8`，没有复制上游文件。同一份 fixture 分四步看会话阻塞、Router / Worker、线程直通和 SQLite 崩溃恢复。模型是确定性 mock，不接 GitHub，也不跑在 DSH 上。Node 从 22.5 起、不到 23。作者写明不是生产框架。MIT.
- [ForkbombEu/superagent](https://github.com/ForkbombEu/superagent) - 🔌 非官方的 Coolify Compose 包，仓库没有 LICENSE。镜像是 Node 24，克隆原型 `main`，全局安装 `@deepseek-ai/dsh@0.2.1-rc.3`。管理接口仍只听 `127.0.0.1:3082`，旁边的 Caddy 把 Host 改回这个地址；外层 Coolify 的 Caddy 对全部路径做 HTTP Basic Auth。README 写明这条路径没有做完端到端测试，也写明默认 `observe` 仍会做名单里的点名请求。不要把 3082 直接公布出去.
- [miyako-sa/dsh-superagent-telegram](https://github.com/miyako-sa/dsh-superagent-telegram) - 🔌 非官方的 Telegram Source 插件。长轮询把私聊、列出的群和频道里的文本和媒体收成 Trigger，经宿主的 Policy 开工单，回复回到原来的聊天和话题。官方仓库没有这个来源，也不导出 Source 类型，所以插件自带要用的那一份，只往宿主的 `orchestrator` 上注册 `telegram`；没有这项服务时保持空转。消息正文仍留文件 id 占位；照片、文档、视频和语音会下载到按令牌分开的目录（Bot API 单文件 20 MB，可关掉），路径记成 Thread 上的 `telegram-files`。回复若带 `attachments`，先发文字，再最多 10 个文件当文档发出（上传 50 MB）。出站文件要宿主有 `orchestrator_send_file`，上游 `6337fc8` 没有。令牌只从环境变量读。`docs/UPSTREAM.md` 把对照机器写成宿主 `0.2.1-rc.3`、DSH `0.2.1-rc.3`、Node 24，上游提交记的是 `6337fc8`。版本 0.1.0。MIT，作者写明与 DeepSeek 无关.
- [hi-unc1e/dsh-superagent-prototype](https://github.com/hi-unc1e/dsh-superagent-prototype) - 🔌 上游 `6337fc8` 的分支，多了一个默认关闭的本机来源 `parallel-chat`。同一条聊天流里的消息各自理解，再交给已有的工单执行器；面板在 `src/client/chat.tsx`。接收开关默认关。README 开头仍是上游原文.
- [kohakushigure/dsh-superagent-lab](https://github.com/kohakushigure/dsh-superagent-lab) - 🔌 独立研究仓库。`local-superagent/` 用 Node 24 内置模块做 Router、Worker、两级过滤、SQLite 和 Watchdog，零 npm 依赖，不跑在 DSH 上，来源只有本机 HTTP 和 CLI。不设密钥时走 mock；`docs/08` 写明那一轮自 loop 产出的是模板提案，真实网关还没测过。README 写明原生 Windows 跑不了官方原型：`stateDir` 拒绝盘符，服务脚本依赖 `fcntl` 和 tmux。MIT，作者写明不是 DeepSeek 的仓库.
- [miyako-sa/dsh-superagent](https://github.com/miyako-sa/dsh-superagent) - 🔌 非官方维护分支，默认分支 `telegram-source`，比上游 `6337fc8` 超前 8 个提交。Telegram Source 已拆到单独的插件。Worker 用 `orchestrator_send_file` 在结果里附最多 10 个文件，单文件 50 MB，缺文件、目录和相对路径会被拒绝；工单即将结束时要把文件放进 `orchestrator_report` 的 `files`，否则工具已经卸掉。给人看的附言是同一次调用的 `files_text`；不写就只发一行文件名，内部 `summary` 不会发给人。不支持文件的来源会忽略该字段。点名请求的卡片多了一句：不算仓库工作的私聊不要忽略，用 `orchestrator_reply` 短答。`scripts/dsh-superagent` 能认出 Node 24 服务进程的 MainThread。README 开头仍是上游原文。不是 DeepSeek 的仓库.
- [NattoCB/dsh-plugin-superagent](https://github.com/NattoCB/dsh-plugin-superagent) - 🔌 DSH 上的项目控制台，不是原型的分支。项目、Ticket、Assignment 和 DSH Session 分开存；输入必须点名项目，不用模型猜归属，也没有 Watchdog。本机 `3081`，只听 loopback，做完要人点验收。作者在 `docs/verification.md` 里写明用 DSH `0.2.0-rc.2` 跑过一次真实 Worker，截图是虚构项目。版本 0.1.1，peer 钉在 `@deepseek-ai/dsh-llm@0.2.0-rc.2`。和原型的工单模型不同，不要混装当替代品。MIT，不是 DeepSeek 的插件.
- [two-workspaces/dsh-superagent](https://github.com/two-workspaces/dsh-superagent) - 🔌 非官方的 Railway 模板，仓库没有 LICENSE。镜像是 Node 22，钉住原型 `6337fc8` 和 `@deepseek-ai/dsh@0.2.1-rc.3`。管理接口仍只听 `127.0.0.1:3082`；Caddy 做 HTTP Basic Auth，把 Host 改回这个地址并去掉 Origin，`/healthz` 不带密码。构建时把客户端的 `isLoopback` 判断改成恒真，公网域名才能保存模型密钥，边界是这层密码。配置不改接受模式，所以默认仍是 `observe`：README 写明主动接手默认关，允许名单里的点名评论大约一分钟内会出现工单。容器里以 root 跑命令。不要把 3082 直接公布出去.
- [lyra-planet/claude-superagent](https://github.com/lyra-planet/claude-superagent) - 🔌 独立重实现，给 Claude Code 用，不跑在 DSH 上。作者写明领域核心拷自 `6337fc8`，跟进规则多了 `owns`，会话换成 Claude Agent SDK `0.3.310`；Router 没有 shell。Worker 的 Bash 可以进沙箱，写入只限工单目录和草稿，状态库、Worker 的 Git 配置和 shim 不再可写。`PreToolUse` 拦住合并自己的 PR，也拦住 REST merge、`gh` 删除和从家目录起的 Grep 或 Glob。作者在测试仓库上用收窄后的沙箱跑通了一次从请求到 PR。另有终端来源。面板仍在 `127.0.0.1:3082`。包版本 `0.0.0`。README 在 2026-11-02 16:19 UTC 去掉了页首的非官方声明；文末和 `NOTICE.md` 仍写明代码来自原型。MIT，不是 DeepSeek 或 Anthropic 的产品.
- [zhangcun1995521/superagent-mini](https://github.com/zhangcun1995521/superagent-mini) - 🔌 按设计长文做的常驻代理，中文说明写明是净室实现，写的时候不看原型，对照记在 `docs/COMPARE.md`。不做成 DSH bundle：Router 和 Worker 都是 `dsh headless --session-id` 拉起的会话，Router 没有执行工具，模型要吐出一段 JSON（`dispatch` / `ignore` / `reply_only`），每张任务一个 Worker 会话。README 里的常驻入口是飞书。耗时表是作者自己的合成负载，并写明不要引用设计长文的数字。仓库没有 LICENSE。默认分支 `master`，版本 0.1.0。`engines.node` 写 `>=22`，README 说直接跑 TypeScript 要 Node 24。不是 DeepSeek 的仓库.
- [PHLens/dsh-superagent-prototype](https://github.com/PHLens/dsh-superagent-prototype) - 🔌 上游 `6337fc8` 的分支，默认分支是 `dev`，比上游超前 5 个提交。项目工作台会留下对话；工单聚合按版本保存不可变方案和确认（schema 14）。作者在提交说明里写，同一版的确认是幂等的，并用 DSH `0.2.1-rc.5` 对过模型和浏览器。已确认的方案会交给本机 Worker，产物不可变，报告按版本验收，取消会真正停掉 Session；同一份说明写 1325 项测试、类型检查和构建，以及隔离的本机和浏览器验收都通过，持久恢复和备份仍留给后面的 T5。README 开头仍是上游原文，没有写明这是社区分支。MIT，不是 DeepSeek 的仓库.
- [Aily-DSH/dsh-superagent-manager](https://github.com/Aily-DSH/dsh-superagent-manager) - 🔌 DSH 插件，在同一个客户端里管多个原型项目。上游一个实例只盯一个仓库，所以每个项目单独起一个 `dsh web`，端口、stateDir 和 profile 都分开，再代理进 `<id>.localhost` 的标签页。管理 API 在 `/api/dsh-superagent-manager`，只接受回环地址。进程控制用 `taskkill` 和 `netstat`，作者写明目前是 Windows。`engines.dsh` 为 `>=0.2.1-rc.2`。不设 `SUPERAGENT_MANAGER_OPERATOR` 时，新建实例写入的操作者是 `SolaNoKan`。版本 0.3.0。MIT，不是 `deepseek-ai` 的插件.
- [cheng200606/dsh-superagent](https://github.com/cheng200606/dsh-superagent) - 🔌 非官方分支，比上游 `6337fc8` 超前 16 个提交。`local/` 里有替代 tmux 服务脚本的 Windows 进程守护，以及可重放的宿主补丁。根目录的 `src/` 已不是未改的上游：本地目录来源把文件夹里的文件当成请求，答案写在旁边的 `.reply.md`；回环上的 `POST /orchestrator/webhook` 要带 `x-webhook-request: 1`，共享令牌可选，不设则只信回环，结果没有回程；执行页可以新建工单，走内置的 `manual` 来源，算操作者自己的点名请求。每次 schema 迁移前用 `VACUUM INTO` 留备份，失败时库文件保持原样。配置脚本把 prepare 的 fetch 指到 `ghfast.top` 的只读镜像，不是 006 里的取代码方式。README 开头仍写根目录是未改的上游。`engines.dsh` 仍是 `^0.2.1-alpha.2`。MIT，不是 DeepSeek 的仓库.
- [alnyx-dev/dsh-superagent-windows](https://github.com/alnyx-dev/dsh-superagent-windows) - 🔌 非官方的 Windows 移植，相对 `6337fc8` 改了平台路径、命令查找和部署文档，并换掉服务脚本。`scripts/dsh-superagent.mjs` 在 Windows、Linux 和 macOS 上管同一个 profile，不调用 tmux 或 `fcntl`；Windows 上 `stop` 靠 `NODE_OPTIONS` 里的钩子在进程内发 SIGINT，因为不能向另一个进程投递。编排会话的工具白名单在 Windows 上写 `pwsh`，其它平台写 `bash`；若仍写死 `bash`，`tools.restrict()` 会因为 profile 没注册这个工具，编排会话每次恢复都失败。文档写 Windows 10/11 不需要 WSL、Git Bash、Python 或 tmux，自带 PowerShell 5.1 即可；命令查找先找 `.exe`，再找 npm 的 `.ps1`，因为进程接口不能直接启动 `.cmd`。没有公开的端到端运行记录。`package.json` 的 author 仍是 DeepSeek Harness Team，README 仍用上游第一人称，`engines.dsh` 仍是 `^0.2.1-alpha.2`。MIT.
- [ChrisCindy/dsh-superagent-prototype](https://github.com/ChrisCindy/dsh-superagent-prototype) - 🔌 上游 `6337fc8` 的社区分支，比上游超前 2 个提交，不是 DeepSeek 的仓库。后一笔把 Policy、接受模式、prepare 命令和可写根改成只能在面板上保存，主会话的预设改成只读；Worker 只能写工单目录、临时区和配置过的可写根，`setup` 会拒绝把状态目录、`$DSH_HOME` 或 profile 放进可写根。合并走宿主上的 `orchestrator_merge_request`，Worker 的 `gh` 拒绝 merge。`docs/009` 写明这层只拦住写，不拦住读，同一个系统用户仍能读到凭证；`sandbox: full` 会关掉文件限制。更早的一笔只是飞书健康检查在 ready 之后仍保留错误。`engines.dsh` 仍是 `^0.2.1-alpha.2`。README 开头仍是上游原文。MIT.
- [Hewlbern/superOpenAgent](https://github.com/Hewlbern/superOpenAgent) - 🔌 按设计长文做的独立 TypeScript harness，不跑在 DSH 上，作者写明与 DeepSeek 无关。Router 没有 shell，Worker 隔离执行，另有 Watchdog、异步 `start()` / `stop()`、文件状态和本机环境接口；分片、长期记忆、实例间通信和凭证隔离还没有。快速开始是本机终端频道，没有接上 GitHub 或飞书来源。README 引用的 12.4 / 15.7 分钟是设计长文里的合成实验，不是这个仓库的测量。Node 从 20 起，包名 `super-open-agent`，版本 0.1.0。MIT.
- [Meaple-SFKY/Super-Agent-Plugin](https://github.com/Meaple-SFKY/Super-Agent-Plugin) - 🔌 非官方的 DSH bundle，包名 `@meaple-sfky/dsh-super-agent`，版本 0.1.0，`engines.dsh` 钉在 `^0.2.1-rc.5`，`package.json` 标了 `private`。Router 预设不装 shell，Worker 的前台命令 5 秒转后台；行 id 加 `sa-` 前缀，避免和上游 bundle 撞车。委托出去的会话（Worker 和它再开的 subagent）在创建时写成 `danger-full-access`，审批策略是 `never`；作者写明一个 Worker 写到了工作区外的 `$DSH_HOME`。状态按 `DSH_HOME` 分，不按 profile 分，Trigger 要单独的 GitHub 身份。description 仍写 skeleton。仓库没有 LICENSE，不是 `deepseek-ai` 的插件.

## 生态

跑 superagent 时偶尔会一起用到的。皮肤和挂件不收。和本原型的工单模型不同的，不要混装当替代品。

### 飞书

和原型自带的飞书 Source 不是同一个东西。

- [omdsh-dev/dsh-lark](https://github.com/omdsh-dev/dsh-lark) - 🔌 DSH 的飞书 / Lark IM 通道.
- [PGZXB/dsh-feishu](https://github.com/PGZXB/dsh-feishu) - 🔌 飞书里的 DSH 控制台，卡片审批，扫码配置.
- [PlutoKeating/dsh-lark-bot](https://github.com/PlutoKeating/dsh-lark-bot) - 🔌 飞书桥，流式卡片，多角色。AGPL-3.0.

### GitHub 与工作流

- [PerryLink/dsh-github](https://github.com/PerryLink/dsh-github) - 🔌 GitHub CI、带人工批准的 PR 评审机器人。Apache-2.0.
- [omdsh-dev/dsh_workflow](https://github.com/omdsh-dev/dsh_workflow) - 🔌 把一次性多 Agent 调度做成可保存、可恢复的工作流.
- [toolclub/dsh-agent-team-gui](https://github.com/toolclub/dsh-agent-team-gui) - 🔌 持久的多模型工作流小组.
- [GZX2211/dsh-Visual-Workflow](https://github.com/GZX2211/dsh-Visual-Workflow) - 🔌 可视化编排，含人工确认.
- [john-walks-slow/dsh-proactive](https://github.com/john-walks-slow/dsh-proactive) - 🔌 由模型预约的主动跟进，唤醒冷会话。和 superagent 的 Policy / Ticket 不是同一套.

### 命令行

- [GitHub CLI](https://cli.github.com/) - 🛠️ 原型要求 `gh` 2.48+.

## 讨论

- [Node 24 下 status 看不到进程，晚到的 GitHub 提及可能被游标跳过](https://github.com/deepseek-ai/deepseek-harness/discussions/11700) - 📝 操作向的讨论。[#11805](https://github.com/deepseek-ai/deepseek-harness/discussions/11805) 补充：`stop` 会打印已停止，进程和端口其实还在。同仓库的 #11774、#11781 是补丁.
- [通知看起来正常，提及却可能进不来](https://github.com/deepseek-ai/deepseek-harness/discussions/11706) - 📝 按 SETUP.md 装好后，作者把一个私有仓库的 Issue 做到了 PR。GitHub 来源可以一直显示就绪，通知数却是 0。`LANG=zh_CN.UTF-8` 时，测试对不上 Git 的英文文案。机器上另一份更旧的 `gh` 若从系统钥匙串取令牌，操作者自己的 Git 会改用智能体账号认证.
- [重试和 prepare 可以活过它们的 Assignment](https://github.com/deepseek-ai/deepseek-harness/discussions/11801) - 📝 对照 `6337fc8` 的 `src/executor/dsh.ts`。提供方重试可以在 Assignment 停掉之后仍把内容交给 Worker。Ticket 的停止取消不了还没有 Worker 的 prepare。失败的 prepare 留下目录后，重试会把那个目录当成已经准备好.
- [Windows 上 Worker 会话没有 shell 和文件工具](https://github.com/deepseek-ai/deepseek-harness/discussions/11905) - 📝 社区报告，不是上游公告，也还没有回复。作者写明在 Windows 11 上、用 DSH CLI `0.2.1-rc.3`，把原型 `0.1.0` 装进专用 profile：Router 能跑 `pwsh`，GitHub 来源能开工单，prepare 也能克隆；Worker 会话只有编排器工具和 `subagent`，没有 shell 和文件工具，深度 1 的 subagent 也只有 `subagent`。同一篇还记下盘符 `stateDir` 被拒、探测用 Unix `env`，以及上游服务脚本依赖 `fcntl` 和 tmux.

## Contributing

欢迎补链接，规则和标记的用法见 [CONTRIBUTING.md](CONTRIBUTING.md)。
