# Awesome Super Agent

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> DeepSeek Harness（DSH）里 Super-agent 的精选清单：它是什么、设计文章、按顺序读的官方文档、部署攻略、社区解读，以及真正用得上的周边资源。

Super-agent 在这里指 [dsh-superagent-prototype](https://github.com/deepseek-ai/dsh-superagent-prototype)：一个跑在 [DeepSeek Harness](https://www.deepseek.com/en/harness/) 上的研究原型。它盯住 **一个** github.com 仓库（可选再接飞书），只接允许名单里的人派的活，每张工单在独立 Session 和目录里做完，再回到原来的 Issue 或对话里交付，通常是一个 Pull Request。面板只把需要人处理的事摆出来。

原则是：尽可能少占用人的注意力，尽可能多给出价值。它只在被点名请求时行动，或在团队写成规则、并且接受模式允许时行动。

公开仓库第一句就写明：这是研究原型，不是 DeepSeek 正式产品；发布前改写过，所以和内部版本、以及设计文章里的实现都不是同一份代码。文章里的一部分设计是未来方向，原型里没有。

资料核对日期：2026-11-02。星数会过期，下面不写。

## Contents

- [先读这五篇](#先读这五篇)
- [官方](#官方)
- [设计文章](#设计文章)
- [文档地图](#文档地图)
- [部署攻略](#部署攻略)
- [概念速查](#概念速查)
- [社区解读](#社区解读)
- [衍生与移植](#衍生与移植)
- [DSH 生态](#dsh-生态)
- [讨论](#讨论)
- [Contributing](#contributing)

## 先读这五篇

1. [README](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/README.md) — 它是什么，以及它不是什么。
2. [000 Philosophy](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/000-philosophy.md) — 注意力原则：何时行动、何时开口、操作者要负什么责。
3. [002 Authorization](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/002-authorization.md) — `off` / `observe` / `auto`。默认 `observe` 不是暂停，点名请求照样会做。
4. [006 Deployment](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/006-deployment.md) — 账号、安装、启动、Management API、排错。
5. [设计长文（微信）](https://mp.weixin.qq.com/s/zoS7GlReWPhfCjENGxXCdg) — Router / Worker 为什么要拆开。读的时候记住：这是架构讨论，不是当前 Harness 的功能清单。

装的时候把 [SETUP.md](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/SETUP.md) 交给一个编程智能体。它一次问一个问题，先给一份计划，再安装。密钥不要贴进对话。

## 官方

- [deepseek-ai/dsh-superagent-prototype](https://github.com/deepseek-ai/dsh-superagent-prototype) - 面向单个 GitHub 仓库的主动式编程智能体，DSH bundle。MIT，DeepSeek Harness Team，2026。
- [SETUP.md](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/SETUP.md) - 给智能体看的安装剧本：探测环境、逐个提问、一份计划、再通过 Management API 落配置。
- [LICENSE](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/LICENSE) - MIT，Copyright (c) 2026 DeepSeek Harness Team。
- [DeepSeek Harness](https://www.deepseek.com/en/harness/) - 产品页。全球公开预览，Cordis 上「万物皆插件」。这一页不介绍 Super-agent。
- [deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness) - Harness 本体，MIT。外部贡献走 Discussions 和带 `dsh-plugin` topic 的插件，不接受外部 PR。见 [CONTRIBUTING.md](https://github.com/deepseek-ai/deepseek-harness/blob/master/CONTRIBUTING.md) / [中文](https://github.com/deepseek-ai/deepseek-harness/blob/master/CONTRIBUTING.zh.md)。
- [Harness 文档](https://deepseek-harness.github.io/deepseek-harness/) - 文档站。[Quickstart](https://deepseek-harness.github.io/deepseek-harness/en/guide/quickstart)、[插件市场](https://deepseek-harness.github.io/deepseek-harness/en/guide/plugin-market)。
- [`@deepseek-ai/dsh`](https://www.npmjs.com/package/@deepseek-ai/dsh) - CLI：profile、插件、配置。原型安装块钉的是 `0.2.1-rc.3`；`package.json` 的 `engines.dsh` 是 `^0.2.1-alpha.2`。2026-11-02 登记的 `latest` 是 `0.2.1-rc.5`（满足 engines），`0.2.2-alpha.2` 不满足。跟部署文档走。

## 设计文章

文章和仓库是 2026-10-31 晚上一起出来的。deepseek.com 和 arXiv 上没有对应正文。英文标题 *Exploring Super-agent Design: Towards a More Autonomous Agent System* 是中文长文的机器翻译，仓库 README 引用了这个英文名，但没有给出链接。

- [Super-agent 设计探索：迈向更自主的 Agent 系统](https://mp.weixin.qq.com/s/zoS7GlReWPhfCjENGxXCdg) - 中文原文。常驻 Router 只做路由，Worker 隔离执行；Trigger 的快过滤和慢过滤；Thread 订阅直通；Inline & Spill；Chain of Recall；投机执行但默不作声；全双工 `start()` / `stop()`（文中说 Harness 已有，默认不开）；IM 回复大约限制在 100 词；云端无状态 Agent Loop、数据库里的会话、无状态边缘守护进程（BYOM）；外部 Watchdog 可以判断跑偏并改提示词。文末写明原型只实现了其中一条路径，不是产品。署名 Yujia Qiao，DeepSeek Harness team。
- [知乎转载副本](https://blog.eimoon.com/p/deepseek-super-agent-design/) - 微信打不开时的全文副本，保留了「这不是现有功能说明」的声明。
- [英文机器翻译](https://threadnavigator.com/thread/2116857257245987236/) - 第三方标注为 @DeepSeekHarness 帖子的机器翻译，含同一组实验数字。不是官方英译。原帖 [x.com/DeepSeekHarness/status/2116857257245987236](https://x.com/DeepSeekHarness/status/2116857257245987236)。
- [IT之家](https://www.ithome.com/1/020/615.htm) - 2026-10-31 21:36 的报道，链到仓库。
- [量子位](https://www.qbitai.com/2026/10/506904.html) - 2026-10-31 21:49 的报道，链到仓库。

文章里有、公开原型里没有的：Watchdog 会话、云端循环加边缘守护进程、BYOM、异步 ReAct 那套 `start()` / `stop()`。仓库里有的是 Engine、Ticket、Eval，以及 Router / Worker 两套 preset。`docs/003` 会把前台 shell 在 5 秒后转成后台任务，这和文章里的云端守护进程不是一回事。

文中实验是合成负载：大约 300 条消息、大约 20 个真实请求，编码换成计时器，跑 5 次。Super-agent 的 p50/p95 是 12.4/15.7 分钟、缓存读 23.2M token；中性单会话是 20.4/29.5 分钟、6.1M。不要把这组数字当成真实仓库上的生产力结论。

## 文档地图

编号文档要按顺序读，后一篇只用前面已经定义过的概念。

| 文档 | 你能得到什么 |
| --- | --- |
| [000 Philosophy](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/000-philosophy.md) | 注意力原则，以及操作者要收窄名单、减少噪音、为它发出的话负责。 |
| [001 Domain model](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/001-domain.md) | 概念表、Trigger → Eval → 执行的流向、被否掉的概念、中英对照。 |
| [002 Authorization](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/002-authorization.md) | 谁能开工、能走多远。门禁和订阅扇出是两条路。接受模式、Policy 版本、恢复和停止。 |
| [003 Execution](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/003-execution.md) | 一张工单怎么执行、交付、恢复。一工单一 Session、一目录；prepare 命令；并发；自动合并只是提示词；工单何时结束。 |
| [004 Prompts](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/004-prompts.md) | 智能体读什么。团队可改的三段、技能覆盖、组装顺序、提示词版本不可变。 |
| [005 Panel](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/005-panel.md) | 侧栏面板：现在要不要我、系统是否健康、一张工单从 Trigger 到 Ticket 的历史。 |
| [006 Deployment](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/006-deployment.md) | 安装、账号、机器配置、启停、设置、Management API、升级、排错。 |
| [007 Architecture](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/007-architecture.md) | 概念到 Runtime、Engine、类和源文件的开发者地图。 |
| [008 Cookbook](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/008-cookbook.md) | 用一个虚构的 webhook Source 演示怎么加一种来源。 |

提示词在 [`prompts/`](https://github.com/deepseek-ai/dsh-superagent-prototype/tree/main/prompts)。团队通常改这三份：[`team/communication.md`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/prompts/team/communication.md)（何时开口）、[`team/scope.md`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/prompts/team/scope.md)（何时做到可评审的 PR，何时停成草稿）、[`team/delivery.md`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/prompts/team/delivery.md)（提交署名和自动合并）。技能：[`dsh-superagent-github`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/skills/dsh-superagent-github/SKILL.md) 用 `gh` 读 Issue、PR、评审和 CI；[`dsh-superagent-feishu`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/skills/dsh-superagent-feishu/SKILL.md) 用机器人身份读飞书消息和文档。

## 部署攻略

官方步骤以 [006](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/006-deployment.md) 为准。下面是照着做时最容易踩错的地方。

### 你需要什么

- macOS 或 Linux。服务脚本用 `tmux` 和 `fcntl`，Windows 原生跑不起来。要在 Windows 上跑，看下面的移植。
- Node.js 22.19+ 或 24+，pnpm，git 2.32+，Python 3.8+，tmux。
- GitHub CLI `gh` 2.48+。只支持 github.com，不支持 GitHub Enterprise Server。
- 一个**专门的** GitHub 账号给智能体评论、推分支、开 PR。用只登录了这个账号的 `gh` 包装。GitHub App 不能代替这个账号，因为收件读的是该用户的通知。
- DSH 里配好的模型。Eval 和 Execution 没另指名时，用 profile 的默认模型。
- 飞书可选：`lark-cli`（文档用 1.0.90 测过）和一个带机器人的飞书 / Lark 应用。[larksuite/cli](https://github.com/larksuite/cli)，[飞书 CLI 文档](https://open.feishu.cn/document/mcp_open_tools/feishu-cli-let-ai-actually-do-your-work-in-feishu)。
- 仓库若要按工单开 worktree，本机留一份 clone。用了 Git LFS 就装 `git-lfs`。

### 隔离

`dsh-superagent-worker` 和 `dsh-superagent-router` 以操作者的系统用户、`danger-full-access` 运行。账号包装只是切换凭证，不隔离进程。Worker 能按路径执行程序，也能读家目录。要隔离，就用一个只放智能体凭证的专用系统账号。这两个 preset 只给 dsh-superagent 用，不要选进个人 Session，也不要关掉它们的配置行。

### 安装

在还没有 `superagent` profile 的机器上跑一次。已有 profile 时不要重跑 `dsh superagent --from-default-profile`，那条命令会换掉 profile 并删掉 `cordis.patch.yml`。

```sh
npm install -g @deepseek-ai/dsh@0.2.1-rc.3
git clone https://github.com/deepseek-ai/dsh-superagent-prototype.git dsh-superagent
cd dsh-superagent
pnpm install && pnpm build
dsh superagent --from-default-profile web --dump-config > /dev/null
dsh plugin --profile superagent add "link:$PWD"
```

然后把 [`examples/profile.cordis.patch.yml`](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/examples/profile.cordis.patch.yml) 覆盖到 `~/.dsh/profiles/superagent/cordis.patch.yml`，换掉占位符。示例把面板放在 `127.0.0.1:3082`。`DSH_HOME` 可以改掉默认的 `~/.dsh`。

不要用「装进日常 web profile」的一句话插件命令。部署文档要求单独的 `superagent` profile，并且先在本地 `pnpm build`，再用 `link:$PWD`。

### 启动

```sh
scripts/dsh-superagent start
scripts/dsh-superagent status
```

`start` 在 tmux 会话 `dsh-superagent` 里跑 `dsh --profile superagent`，最多等一分钟。`stop` / `restart` 会等 Executor 优雅退出，最多五分钟，不会强杀，也不会动别的 tmux 会话。

打开日志里的 URL，侧栏进 **Superagent**。在来源、名单、执行环境配好之前，Overview 会告诉你缺什么。

### 第一次派活

1. 设置里的 GitHub 来源填 `owner/name`，CLI 填包装脚本的绝对路径。保存时会检查 CLI 能不能用、账号有没有推送权限，不过就拒绝保存。
2. 允许名单先只加你自己。名单是空的，任何请求都不会变成工单。
3. 执行环境填工单目录的根，以及从本机 clone 开 worktree 的 prepare 命令。变量名以 `DSH_SUPERAGENT_` 开头，示例在 006 里。
4. 自动合并先关掉，由人来合。
5. `status` 正常之后，用名单里的账号在 Issue 下发**一条新评论**：`@<机器人登录名> 你的请求`。默认大约每 60 秒轮询一次通知。改已有 Issue 正文、在正文里补一个 @，收不到。
6. 飞书则在已配置的会话里 @ 机器人。默认可以只听提及。

接受模式先留在 `observe`。三种模式：

| 模式 | 没人点名的活 |
| --- | --- |
| `off` | 不考虑。 |
| `observe`（默认） | 会评估，只记下「本来会接」，不执行。 |
| `auto` | 评估通过、且命中团队写好的规则，就接。 |

三种模式下，允许名单上的人直接点名都有效。名单上的机器人最多提出候选。消息本身改不了名单、规则和授权范围。

### 面板和 API

面板标签：Overview、Source、Trigger、Eval、Execution、Settings。人可以在面板里重试或停止工单。本地 Management API 基址是 `http://127.0.0.1:<port>/orchestrator/api/`，请求头要带 `x-orchestrator-request: 1`，只接受本机 Host。具体路由和示例 `curl` 在 006。

### 常见坑

- 第一次 `start` 以退出码 3 结束，是服务已经起来、但在等配置，不是装坏了。006 的四档：0 正常；1 没在跑、重复实例、无响应或运行时错误，重启可能修好；2 在跑，但来源或 Policy 同步这类部件报错，重启修不好；3 在跑并等待设置，重启修不好。社区笔记把这次经历写下来了：[中文](https://blog.laozhang.ai/zh/posts/deepseek-super-agent-github) / [英文](https://blog.laozhang.ai/en/posts/deepseek-super-agent-github)。那是 API 经销商的博客，作者写明没有真的派完一单；退出码以 006 这张表为准。
- 没写 `stateDir` 时，面板可能停在别的端口，而不是示例里的 3082。
- 升级时编辑现有的 `cordis.patch.yml`。不要重新执行 profile 创建命令。
- Node 24 上 `ps` 的 `comm` 可能显示成 `MainThread`，`status` 会打印空的 `pids` 并以 1 退出，tmux 里其实是健康的。见 [deepseek-harness#11700](https://github.com/deepseek-ai/deepseek-harness/discussions/11700)。原型仓库关了 Issues，这类讨论在 Harness 仓库。
- 通知游标有可能永远跳过一条晚到的提及，同一条讨论里有描述。

## 概念速查

定义来自 [001](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/001-domain.md)，一行一个。

| 概念 | 是什么 |
| --- | --- |
| Source 来源 | 事件流。记住读位置和机器人身份，丢掉自己的消息和范围外事件，再把 Trigger 译成 Thread 和回复地址。 |
| Trigger 触发 | 一条不可变事件。同一条接受两次，只产生一张工单。 |
| Thread 线程 | 按时间排好的 Trigger。同一条上同时最多一次 Eval。 |
| Correlation 关联 | 一条 Trigger 所属的多个 Thread 之间的关系。只补上下文，不负责路由。 |
| Channel 渠道 | Thread 的写侧。确认、回复、PR、处理完的评审，都是写。 |
| Policy 策略 | 带版本的评估规则，加上每个来源自己的名单和接受模式。不同来源的身份互不映射。 |
| Eval 评估 | 对一批 Trigger 的无状态判断，先门禁再评估。只有它能创建工单。 |
| Mandate 授权 | 一次接受所授予的范围和强度，不可变。范围变大就要新工单。 |
| Ticket 工单 | 一份已授权的工作。open 与 ended 之间可以因人的消息恢复，除非被人停掉。 |
| Assignment 派工 | 工单和某一个 Executor 实例的绑定。一张工单在时间上可以有多次。 |
| Subscription 订阅 | 工单和 Thread 的多对多。匹配的 Trigger 扇出给订阅者，不再检查是谁发的，也不再走 Eval。 |
| Executor 执行器 | 真正跑派工的端口：一工单一 Session、一目录。实现上是 DSH Session。 |

订阅上的后续消息是上下文，不带来新的授权。Worker 没有向人提问的通道；要人做决定时，走结构化的 attention，一次一个具体问题。

## 社区解读

官方文档优先。下面这些是读完文档之后，用来对照「文章写了什么、仓库做了什么」。

### 设计与代码的差别

- [dsh-superagent 深挖报告](https://www.bytenote.net/article/dsh-superagent-deep-dive-report) - 把实验条件、默认 `observe`、以及「隔离是 Session 加目录，不是沙箱」写清楚了。
- [读缓存涨到 3.8 倍，P95 少 14 分钟，云端那半还只在文章里](https://halfatoken.com/insights/deepseek-super-agent/) - 对同一组实验做反算：相对「倾向委派」的单会话，p50 差距大约 1.4 分钟；并在树里没有找到 Watchdog、边缘守护进程或 BYOM。反算是作者自己的，不是 DeepSeek 的结论。
- [Ticket、Mandate、Executor](https://www.harelasaf.com/articles/deepseek-dsh-superagent-prototype-ticket-mandate-executor-design) - 只读了 000–003 和 007。订阅线程上的跟进不授予新权力；Worker 持有那条消息时，主会话只能停止这项工作。作者没有跑代码，也没有读设计长文。
- [MarkTechPost](https://www.marktechpost.com/2026/11/01/deepseek-dsh-superagent-router-worker-coding-agent/) - 英文新闻稿，正确地把 p95 标成合成工具模拟。它把异步 ReAct 和未实现的想法放在一起，读的时候用上面的「文章有、仓库没有」对照。

### 短讲解

- [Piotr Sobolewski（中文）](https://sobolewski.pro/zh/blog/deepseek-superagent) / [English](https://sobolewski.pro/en/blog/deepseek-superagent) - `off` / `observe` / `auto` 的短地图，以及 `danger-full-access` 为什么需要专用系统账号。
- [DeepSeek Super Agent: Complete Guide](https://super-agent.io/deepseek-super-agent/) - 独立站点，声明与官方无关。讲清了 GitHub App 不能替代智能体账号，`observe` 不是把点名请求也暂停。图是概念示意，不是一次真实部署。
- [From a GitHub Request to a Reviewable PR](https://pragmaticai.substack.com/p/deepseek-superagent-from-a-github) - 两分钟：第一单只改 README 的 Quick Start，先不要合并。文中写明视频里的 diff 不是一次真实 DSH 运行的产物。配套视频：[YouTube](https://www.youtube.com/watch?v=7vYILOY6_nE)。
- [掘金：源码拆解 + 本地部署](https://juejin.cn/post/7702737817416105999) - 七牛的文章，把模型指到他们自己的网关。安装顺序可以作索引；@ 的写法和星数以官方文档和仓库为准。
- [OpenFree 条目](https://openfree.tools/zh/dsh-superagent) - 一屏 README 复述，不是部署指南。

### 目录站

- [dsh.plus](https://dsh.plus/en/) - 策展过的 DSH 插件目录，收录了这个原型的一句话简介。站内那个「装进 DSH」的插件是目录插件本身，不是 superagent 的安装方式。
- [awesome-dsh-plugin](https://github.com/awesome-dsh-plugin/awesome-dsh-plugin) - DSH 插件精选，CC0。站点 [awesome-dsh-plugin.com](https://awesome-dsh-plugin.com/)。官方在 [Harness discussion #215](https://github.com/deepseek-ai/deepseek-harness/discussions/215) 指向过它。
- [billLiao/awesome-dsh-plugin](https://github.com/billLiao/awesome-dsh-plugin) - 按 GitHub topic `dsh-plugin` 收来的大清单，含弱相关。浏览站：[billliao.github.io/awesome-dsh-plugin](https://billliao.github.io/awesome-dsh-plugin/)。要找 superagent，用上面那份策展清单，而不是这份主题抓取。

## 衍生与移植

这些不是 `deepseek-ai` 名下的代码。

- [saltapp/dsh-superagent-prototype-cn](https://github.com/saltapp/dsh-superagent-prototype-cn) - 社区中文文档。
- [buyou-claw/dsh-superagent-win](https://github.com/buyou-claw/dsh-superagent-win) - Windows 移植。上游只在 POSIX 上启动；这一份把 `PATH` 和 git 配置收到单条命令上。prepare 要用 PowerShell（`$env:DSH_SUPERAGENT_DIR`），不能用 `sh`。测试仍按 Linux 写。
- [JHOpenDev/dsh-superagent-inbox](https://github.com/JHOpenDev/dsh-superagent-inbox) - 给原型加的文件收件箱 Source：一个目录驱动它，不需要外部账号。
- [MYCF711/dsh-session-superagent](https://github.com/MYCF711/dsh-session-superagent) - 会话级：盯一个 GitHub 仓库，有变化就唤醒会话。自述凭证不进模型。
- [vicfei/dsh-super-agent](https://github.com/vicfei/dsh-super-agent) - 独立重实现，给 Claude Code、Codex、opencode 用，不跑在 DSH 上。默认 `observe` 只打印 WOULD-ACCEPT。

## DSH 生态

跑 superagent 时偶尔会一起用到的，不是皮肤和挂件。

飞书通道，和原型自带的飞书 Source 不是同一个东西：

- [omdsh-dev/dsh-lark](https://github.com/omdsh-dev/dsh-lark) - DSH 的飞书 / Lark IM 通道。
- [PGZXB/dsh-feishu](https://github.com/PGZXB/dsh-feishu) - 飞书里的 DSH 控制台，卡片审批，扫码配置。
- [PlutoKeating/dsh-lark-bot](https://github.com/PlutoKeating/dsh-lark-bot) - 飞书桥，流式卡片，多角色。AGPL-3.0。

GitHub、多人或主动行为，和本原型的工单模型不同，不要混装当替代品：

- [PerryLink/dsh-github](https://github.com/PerryLink/dsh-github) - GitHub CI、带人工批准的 PR 评审机器人。Apache-2.0。
- [omdsh-dev/dsh_workflow](https://github.com/omdsh-dev/dsh_workflow) - 把一次性多 Agent 调度做成可保存、可恢复的工作流。
- [toolclub/dsh-agent-team-gui](https://github.com/toolclub/dsh-agent-team-gui) - 持久的多模型工作流小组。
- [GZX2211/dsh-Visual-Workflow](https://github.com/GZX2211/dsh-Visual-Workflow) - 可视化编排，含人工确认。
- [john-walks-slow/dsh-proactive](https://github.com/john-walks-slow/dsh-proactive) - 由模型预约的主动跟进，唤醒冷会话。和 superagent 的 Policy / Ticket 不是同一套。

前置 CLI：[GitHub CLI](https://cli.github.com/)。

## 讨论

- [Node 24 下 status 看不到进程，晚到的 GitHub 提及可能被游标跳过](https://github.com/deepseek-ai/deepseek-harness/discussions/11700) - 操作向的讨论。同仓库附近的 #11706、#11774、#11781 是补丁，不是指南。

## Contributing

欢迎补链接。一条一条来：能打开的 URL、一句话说明它比官方文档多给了什么、它和原型的关系（官方 / 解读 / 移植）。设计文章里的能力，请标明原型有没有实现。

不收：DeepSeek API Key 教程、与这个 bundle 无关的 Harness 皮肤、没有读过正文的新闻转述、以及和 [006](https://github.com/deepseek-ai/dsh-superagent-prototype/blob/main/docs/006-deployment.md) 矛盾的一行安装命令。
