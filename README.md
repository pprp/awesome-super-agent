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
- [vicfei/dsh-super-agent](https://github.com/vicfei/dsh-super-agent) - 🔌 独立重实现，给 Claude Code、Codex、opencode 用，不跑在 DSH 上。默认 `observe` 只打印 WOULD-ACCEPT.
- [LofiSu/deepseek-super-agent](https://github.com/LofiSu/deepseek-super-agent) - 🔌 独立重实现，自带薄 Harness 和 `ds-agent`。Router / Worker、Trigger 和 Watchdog 不跑在 DSH 上，作者写明不是 DeepSeek 产品.
- [cjy851096/nano-dsh-superagent](https://github.com/cjy851096/nano-dsh-superagent) - 🔌 约 3400 行的独立骨架，用来对照设计长文里原型没有的 Watchdog、异步 `start()` / `stop()` 和 Inline & Spill。不跑在 DSH 上；文中的批处理压测是作者自己的.
- [HulnotHutu/dsh-superagent-qq](https://github.com/HulnotHutu/dsh-superagent-qq) - 🔌 上游树上的 QQ 群来源。配置过的群里，经 OneBot v11 进来的消息变成 Trigger，回复回到群里。代码在 `src/sources/qq.ts`，说明在 `docs/009-qq-source.md`，本地小工具是 `napq`。作者写明与腾讯无关，用第三方客户端连接 QQ 可能违反用户协议.
- [Check-mj/dsh-superagent-windows](https://github.com/Check-mj/dsh-superagent-windows) - 🔌 Windows 适配，基线 `6337fc8`，差量在 `WINDOWS-DELTA.md`。另有设计长文里的 Watchdog：独立 Session，默认定时 30 分钟看工单是否停滞，设为 0 则关闭（`src/app/watchdog.ts`）。Router 分片默认关；打开后同一条 Thread 固定进同一个 Router，空分片到第一次投递才创建（`src/app/shards.ts`）。桌面壳不引用上游源码.
- [kulebbb/dsh-superagent](https://github.com/kulebbb/dsh-superagent) - 🔌 个人桌面插件，钉在 DSH `0.2.1-rc.5`。侧边栏目前只显示骨架；领域核心和「只有我」的 Policy 已有测试，作者写明还没接到插件入口.
- [shibaeff/dsh-superagent-prototype](https://github.com/shibaeff/dsh-superagent-prototype) - 🔌 上游 MIT 分支，给 ChatGPT 订阅加了一条脚本化的 Codex 登录。`codex-install` 默认装 `@jcy2387/dsh-codex-provider`，作者在 DSH `0.2.1-rc.3` 上核对过能列出模型，没有打过模型调用。`codex-install --codex-package dsh-codex-auth@0.3.4-rc.2` 复用本机 Codex CLI 的 `auth.json`，该插件只接受 `0.2.1-rc.1` 和 `rc.2`；作者在 `rc.2` 上记了一次 headless 回答，面板里没有再登录。说明在 `docs/009-codex-auth.md`.
- [mycguo/dsh-superagent-prototype](https://github.com/mycguo/dsh-superagent-prototype) - 🔌 署名 Tebra 的分支，README 写明供内部评估，不是 DeepSeek 的仓库。一个 `superagent` profile 同时盯多个 GitHub 仓库，`scripts/prepare-github-ticket` 按仓库分缓存。本机面板可以加 Google 登录（`docs/009-google-login.md`），不更换模型供应商.
- [alasong/ssa1101](https://github.com/alasong/ssa1101) - 🔌 独立的轻编排（目录名 `dsh-superagent-lite`）。`scripts/server.ts` 默认对每个 assignment spawn `dsh --json`，`SA_WORKER=fake` 才用假会话。README 仍写 SessionFactory 是桩，Cordis bundle 只打挂载日志，准入记账还在外部的 kaa.
- [waixv-superagent/dsh-superagent-prototype](https://github.com/waixv-superagent/dsh-superagent-prototype) - 🔌 上游分支。只读 TAPD 来源默认关闭、不回写；任务中心按租约把工单放进共享工作区，租约不是沙箱。飞书方案卡片在 `src/app/plan.ts`，同一份 `docs/012` 的阶段表仍把 P1 方案门写成未实现。更早的提交把交付改成可切换的 Perforce shelve / Swarm，默认仍是 Git，包装会拒绝 `p4 submit`。README 开头仍是上游原文.
- [Yao-MY06/more-agent](https://github.com/Yao-MY06/more-agent) - 🔌 默认分支 `feat/m0-shadow-run` 把原型引擎钉在 `6337fc8`，放进 `vendor/dsh-superagent`，`src/core` 按清单哈希未改。M0 只影子运行：Leader 用 `claude -p` 选项目，ShadowExecutor 只记账，不启动 Worker，也不发消息。不跑在 DSH 上。MIT.
- [yingpengma/cc-super-agent](https://github.com/yingpengma/cc-super-agent) - 🔌 按设计长文做的独立 Router / Worker，给 Claude Code 用，不跑在 DSH 上，也不是 `6337fc8` 的分支。Watchdog 是作者自己的 cron，每 15 分钟一次。README 里的对话是示意，不是一次真实运行.
- [xyzOvO/dsh-superagent-prototype](https://github.com/xyzOvO/dsh-superagent-prototype) - 🔌 默认分支 `fix/windows-support`。win32 上插件有六处不改就起不来：盘符绝对路径、子进程直接带环境、prepare 走 `cmd.exe`、编排会话白名单用 `pwsh`（否则 `tools.restrict` 让整个插件加载失败）、另写 `gh.cmd` 并在外来文件扫描里跳过 `*.cmd`。作者在提交说明里记了 Windows 11、Node 24 上从 Issue 到 PR 的一次往返。服务脚本仍依赖 `fcntl`，Windows 上改走面板或 HTTP API.
- [0xdrmnhttn13/dsh-superagent-prototype](https://github.com/0xdrmnhttn13/dsh-superagent-prototype) - 🔌 独立的本地调查代理，README 称为 Superagent Investigator。SQLite 里仍是 Trigger、Policy、Mandate、Ticket 和 Assignment，执行换成受限的 Pi 会话，只注册 `sa_context`、`sa_evidence`、`sa_report`。本机仅所有者可连的 Unix socket 收 UTF-8 证据。不跑 DSH，也没有 GitHub 或飞书来源。工具限制在应用层，不是操作系统沙箱。MIT.
- [jwangkun/dsh-superagent](https://github.com/jwangkun/dsh-superagent) - 🔌 上游 `6337fc8` 的社区分支，不是 DeepSeek 的仓库。同一 GitHub 账号写下的 Issue 或评论，只有正文以 `@` 加上自己的登录名开头，才算这个账号自己的点名请求，其余自己写的回复仍跳过；通知流和按仓库轮询走同一条规则。`setup` 只在自检时改 `PATH` 和 `GIT_CONFIG_*`，查完就恢复，避免放进已有 profile 后留在进程里。默认 `stateDir` 是状态目录下的 `state/dsh-superagent`。`package.json` 为 0.1.3，GitHub Release 停在 v0.1.2，npm 上没有名为 `dsh-superagent` 的包。仓库带编译好的 `lib/`。MIT.
- [mitkox/copilot-superagent](https://github.com/mitkox/copilot-superagent) - 🔌 独立的 VS Code 扩展。工单引擎来自原型，执行换成 GitHub Copilot SDK `1.0.28` 的会话，每张工单一个 Git worktree。收件是 Copilot Chat、GitHub 和 Microsoft Teams。版本 0.4.0 删掉了旧的 DSH 运行时、飞书、网页面板和启动器，并要求 Copilot Business 或 Enterprise 账号加上 Copilot CLI `1.0.105`；自定义供应商和个人套餐的推理地址会被拒绝。扩展宿主关掉后服务就停，命令仍用操作者的系统权限。作者写明不是 GitHub、Microsoft 或 DeepSeek 的产品。MIT.
- [aicodeking/dsh-superagent-gui](https://github.com/aicodeking/dsh-superagent-gui) - 🔌 本机网页，指向任意文件夹。已安装的智能体命令行在单独的 Git worktree 或普通副本里干活，目录要等 Merge 或 Apply 才写回。保留 Trigger、Mandate 和默认 `observe`：没被点名的事变成建议，等人动手。故意不带飞书、GitHub 轮询和 cordis 打包。DSH 用 `dsh --profile headless`，作者标成未实测。Node 22.12+，没有运行时依赖，版本 0.1.0。macOS 和 Linux。不是 DeepSeek 的产品。MIT.

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

## Contributing

欢迎补链接，规则和标记的用法见 [CONTRIBUTING.md](CONTRIBUTING.md)。
