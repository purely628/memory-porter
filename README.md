# memory-porter · 记忆搬运工

> 让 AI 编程助手记住你的常用提示与约定 —— 换项目、开新会话，一句「按老规矩来」直接续上。
>
> **No database. No embeddings. No server. 只有 markdown。**

![License](https://img.shields.io/badge/license-MIT-green) ![Format](https://img.shields.io/badge/format-Agent%20Skills-blue) ![Platform](https://img.shields.io/badge/works%20on-ZCode%20%2F%20Claude%20Code%20%2F%20any%20Agent%20Skills%20client-orange)

## 这是什么问题？

如果你同时维护多个项目，一定经历过：

- 每开一个新会话，都要重新贴一遍「先在本地库验证再交付」「这个字段是 varchar 要转字符串再 join」「组件名必须用英文双引号……」
- 换一个项目，上一个项目里辛苦纠正出来的规矩**全部清零**，同样的坑再踩一遍
- `AGENTS.md` / `CLAUDE.md` 只服务单个仓库，跨项目的通用偏好（回复语言、代码风格）没地方放

## 这是什么方案？

**memory-porter** 是一个遵循 [Agent Skills](https://agentskills.io) 开放标准的技能（skill），配合一个**纯 markdown 记忆库**。它给 AI 助手五个动作：

| 动作 | 一句话 |
|---|---|
| **save** 沉淀 | 把当前会话里反复出现的约定提炼入库，按项目分目录存放 |
| **recall** 恢复 | 新会话开工前读取相关约定，直接照做，不用你复述 |
| **migrate** 迁移 | 把约定合并进某个项目的 `AGENTS.md`（受管区块，不碰其他内容） |
| **export** 导出 | 打一个自带指令的「提示包」md，任何 AI 读了就生效 |
| **forget** 遗忘 | 约定过时了？删掉，索引同步更新 |

零安装依赖、不联网、不上传。记忆全部是你用户目录下人类可读可改的 markdown 文件。

```text
~/.zcode/memories/
├── INDEX.md            ← 全部主题索引（通用 / 各项目分组）
├── topics/
│   ├── common/         ← 跨项目通用偏好（沟通方式、代码风格…）
│   └── <项目目录名>/    ← 每个项目一个目录
│       └── <主题名>.md  ← 一个主题一个文件
└── exports/            ← 导出的提示包（自带指令，谁读谁生效）
```

## 30 秒安装

把 `memory-porter/` 整个文件夹复制到你的 AI 工具的技能目录即可：

| 工具 | 用户级目录（所有项目生效） | 项目级目录（仅当前仓库） |
|---|---|---|
| ZCode | `~/.zcode/skills/` | `<项目>/.zcode/skills/` |
| Claude Code | `~/.claude/skills/` | `<项目>/.claude/skills/` |
| 其他 Agent Skills 客户端 | 各自的 skills 目录 | 各自约定 |

```bash
# ZCode 用户示例
git clone https://github.com/purely628/memory-porter.git
cp -r memory-porter ~/.zcode/skills/
```

> 用在 Claude Code / 其他宿主时，把 `SKILL.md` 里的记忆库根路径 `~/.zcode/memories/` 全文替换成你想要的路径（如 `~/.claude/memories/`），只有这一处需要改。

重启会话后，技能即被发现。首次使用任意动作时，它会自动创建记忆库骨架，无需手动初始化。

## 第一次怎么用（5 分钟体验）

**第 1 步 · 沉淀** —— 在正在干活的会话里说：

> 记住这些约定：提交信息用中文，先跑 lint 再 commit。

它会：提炼规则 → 问清属于哪个项目（没说就推断，推断不了才问）→ 写入 `topics/<项目>/xxx.md` → 更新 `INDEX.md` → 回复存了什么。

**第 2 步 · 恢复** —— 新开会话，开工前说：

> 加载记忆，我要继续写后端接口。

它会：读索引 → 挑出通用偏好 + 该项目相关主题（一般 ≤4 个）→ **列出恢复了哪些约定**（方便你纠偏）→ 直接按约定干活。

**第 3 步 · 打包** —— 想把提示带去任何地方时说：

> 打个提示包，全量的。

它会：把选中主题合并成一个自包含的 md（如 `exports/示例项目-全量-提示包-20260929.md`），文件开头带「给读取本文件的 Agent」的指令头。

**第 4 步 · 直接读包（连技能都不用）** —— 以后任何新任务、甚至换一个 AI 工具，开场说：

> 读取并遵循 C:\Users\<你>\.zcode\memories\exports\示例项目-全量-提示包-20260929.md

提示包自带执行指令，任何能读文件的 AI 助手都会把包内约定当作本轮固定规则，**不依赖本技能是否安装**。

## 全部口令

| 你想干嘛 | 中文说法 | 英文说法 |
|---|---|---|
| 沉淀 | 「记住这些约定」「沉淀记忆」「保存常用提示」 | remember these conventions / save to memory |
| 恢复 | 「加载记忆」「按老规矩来」「回忆上次的约定」 | load my memory / as usual |
| 迁移 | 「把记忆迁移到这个项目」「写进 AGENTS.md」 | migrate memory to this project |
| 导出 | 「打个提示包」「导出成一个 md」 | export a prompt pack |
| 遗忘 | 「忘掉 XX 约定」「这条过时了」 | forget the XX convention |

口令不必逐字匹配，意思到了就会触发；技能没触发时，直接点名即可（如「用 memory-porter 保存」）。

## 记忆库文件格式

每个主题一个 md，frontmatter 字段：

```markdown
---
title: 提交与代码风格        ← 主题中文名
tags: [git, style]          ← 便于检索
source: demo-web-app        ← 从哪个项目学来的
updated: 2026-09-29         ← 最后更新日期
applies: 所有涉及提交代码的任务 ← 什么时候该想起它（recall 挑选依据，必填）
---

## 约定

- 提交信息用中文，首行动词开头（为什么：团队规范要求可读性）
```

**命名规范**：主题文件英文 kebab-case；项目目录英文 kebab-case；导出提示包固定为 `<项目中文名>-<范围>-提示包-<YYYYMMDD>.md`（范围 ∈ 全量 / 仅通用 / 主题关键词）。

**入库红线**：只存「下次还成立」的约定；一次性调试细节不入库；**密码、密钥、未脱敏内网地址绝不入库**。

## 设计原则

1. **纯 markdown，零依赖** —— 不装数据库、不跑服务、不联网。记错了？打开文件直接改。
2. **项目隔离 + 全局通用层** —— 项目专属约定进各自目录，跨项目偏好进 `common/`，互不污染。
3. **人始终在环** —— recall 会先列出恢复了什么；forget 先复述再删；冲突的约定不擅自二选一。
4. **宁缺毋滥** —— 每次恢复一般 ≤4 个主题，记忆是帮助不是轰炸。
5. **隐私第一** —— 记忆库只放你的用户目录，不进任何仓库；唯一例外是 migrate 时写入项目 AGENTS.md 的受管区块（有明确边界标记）。

## 和其他方案比

| 方案 | 依赖 | 跨项目 | 人可读 | 适合 |
|---|---|---|---|---|
| `AGENTS.md` / `CLAUDE.md` | 无 | 弱（单仓库） | ✅ | 仓库本身的固定约定 |
| Mem0 / Zep 等 MCP 记忆服务 | 要装服务或上云 | ✅ | ❌ | 海量语义记忆、多智能体共享 |
| **memory-porter** | **无** | **✅** | **✅** | **个人常用提示与约定的沉淀复用** |

同类开源项目：[hanfang/claude-memory-skill](https://github.com/hanfang/claude-memory-skill)（同为极简 markdown 方案，它的分层思路值得一看）；本项目的差异点是**按项目分目录 + 提示包导出**——提示包可以脱离技能独立使用，适合多工具切换的用户。

## 常见问题

**装了没触发？** 先重启会话；在技能/命令列表里确认 `memory-porter` 出现；还没触发就直接说「用 memory-porter 记住……」。

**多台电脑怎么同步？** 把 `~/.zcode/memories/` 整个目录放进你的私有 git 仓库或网盘同步即可——它就是一堆 markdown。

**约定写错/过时了？** 说「忘掉 XX」让它删；或者自己打开 md 文件改，改完不用通知它。

**会上传我的数据吗？** 不会。技能本体不联网，所有文件都在本机。若你把记忆库目录放进 git 仓库推送到远端，请先自查 topics 内容里没有敏感信息。

**能团队共享吗？** 可以：把某个项目的 `topics/<项目>/` 提交到仓库里，队友装上本技能即可 recall 同一套约定。

## 卸载

删两处即可干净移除：

1. 技能：删除技能目录里的 `memory-porter/`（如 `~/.zcode/skills/memory-porter/`）
2. 记忆：删除记忆库目录 `~/.zcode/memories/`（**先备份**，这是你积累的全部记忆）

## English TL;DR

**memory-porter** is an [Agent Skills](https://agentskills.io)-standard skill that persists your recurring prompts and conventions into a plain-markdown memory library (per-project folders + a global `common/` layer). Say "remember these conventions" to save, "load my memory" to recall in a new session, "export a prompt pack" to produce a self-contained `.md` that **any** AI agent can follow by simply reading the file — no skill, no server, no database. Copy the `memory-porter/` folder into your client's skills directory (`~/.zcode/skills/`, `~/.claude/skills/`, …) and restart the session. The memory root defaults to `~/.zcode/memories/`; replace that path in `SKILL.md` if you host it elsewhere.

## License

[MIT](LICENSE) © 2026 [Xia Mengnan](https://github.com/purely628)
