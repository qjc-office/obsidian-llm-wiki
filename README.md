[English](./README.en.md) | 中文

# Inspool Wiki for Obsidian

首先感谢 [Andrej Karpathy 的《LLM Wiki》原文](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。这个项目直接受那篇 idea file 启发而来。

Karpathy 提出的核心思想很重要：不要每次提问都让 LLM 从原始文档里重新检索和拼装答案，而是让 LLM 持续维护一个介于你和原始资料之间的、可累计演化的 wiki。原文把这个模式讲得很清楚，也为这个项目提供了出发点。

但这个项目不是对原文的机械照抄。它是在那个方向上，结合 Obsidian、中文知识库实践、Codex/Claude 双代理使用、人工确认归档，以及更明确的工作流约束后，做出的一个可直接落地的版本。

## 这个项目解决什么问题

大多数人用 LLM 管资料，还是在做"每次重新理解一遍"的模式：

- 把一堆文档丢给模型
- 提问时临时检索
- 生成一次性回答
- 回答结束后，结构没有留下来

这个项目走的是另一条路：

- 原始资料进入 `raw/`
- LLM 把资料逐步沉淀到 `wiki/`
- 新资料到来时，不是重新开始，而是更新既有知识结构
- 新问答如果有长期价值，可以继续回填到 wiki

结果是：知识不是每次临时拼出来的，而是被持续"编译"进一个长期存在的 Obsidian wiki。

## 设计来源与本项目的增量改造

相比 Karpathy 原文，这个项目保留了"raw sources + wiki + schema"三层结构，但加入了更明确的实践约束：

- 面向 Obsidian 的目录结构和双链写法
- 面向 Codex 的 [AGENTS.md](./inspool-wiki-zh/AGENTS.md) 和面向 Claude 的 [CLAUDE.md](./inspool-wiki-zh/CLAUDE.md)
- 已封装好的 `commands/` 与 `skills/`，放在 `.claude/` 和 `.codex/` 目录下
- 明确的 `unprocessed -> pending_review -> processed` 状态机
- 用户确认后才归档 raw，而不是 ingest 后直接迁移
- 支持小批量相关素材作为一个 ingest 单元
- 支持用户手动分类移动 `concepts / entities / synthesis` 页面
- 增加 lint 健康检查，用于发现旧路径残留、弱证据结论、缺失关系字段等问题

## 目录结构

```text
.claude/
├─ commands/              # Claude Code 命令
└─ skills/                # Obsidian 官方 skill（通用）
.codex/
└─ skills/
   ├─ {core}/             # 核心 skill（5 个）
   └─ {obsidian}/         # Obsidian 官方 skill（通用，5 个）
inspool-wiki-zh/
├─ AGENTS.md
├─ CLAUDE.md
├─ raw/
│  ├─ unprocessed/
│  ├─ processed/
│  ├─ assets/
│  ├─ index.md
│  └─ README.md
└─ wiki/
   ├─ sources/
   ├─ entities/
   ├─ concepts/
   ├─ synthesis/
   ├─ meta/
   ├─ index.md
   └─ log.md
```

目录职责：

- `.claude/commands/`：Claude Code 命令模板。
- `.codex/skills/`：Codex 核心 skill + Obsidian 官方 skill。
- `.claude/skills/`：Obsidian 官方提供的通用 skill。
- `raw/`：原始资料层。原文默认只读，允许维护少量流程元数据。
- `wiki/`：结构化知识层。来源页、实体页、概念页、综合页都在这里。
- `AGENTS.md` / `CLAUDE.md`：代理执行规则入口。

## 推荐采集方式

最推荐的采集方式是使用 **Obsidian Web Clipper**。

标准做法：

1. 安装 Obsidian Web Clipper 浏览器扩展
2. 把剪藏保存目录配置到 `inspool-wiki-zh/raw/unprocessed/`
3. 剪藏网页后，让代理从 `raw/unprocessed/` 开始 ingest

可选建议：

- 如果文章图片有价值，可以把附件下载到 `raw/assets/`
- 尽量保留原始标题、原始链接、日期等上下文
- raw 文件正文尽量不要人工改写

## 标准工作流

### 1. 采集

把原始资料放进 `raw/unprocessed/`。

素材可以是：

- 网页剪藏
- 文章或书摘
- 会议纪要
- PDF 转写稿
- 其他长期有沉淀价值的 markdown 文档

### 2. Ingest

运行 `ingest_raw`。

它会做这些事：

- 读取下一个 ingest 单元
- 创建或更新来源页
- 更新相关实体页、概念页、综合页
- 更新 `wiki/index.md`
- 写入 `wiki/log.md`
- 更新 `raw/index.md`
- 把本批 raw 标记为 `pending_review`

### 3. 审阅

用户审阅这次 ingest 的结果。

重点看：

- 页面命名是否合理
- 双链是否成立
- 结论是否过强
- 是否遗漏关键概念或实体
- 来源支撑是否充分

### 4. Approve

如果确认本次 ingest 没问题，再运行 `approve_ingest`。

它会做这些事：

- 把对应 raw 从 `pending_review` 更新为 `processed`
- 将 raw 文件迁移到 `raw/processed/`
- 回补来源页中的 raw 路径引用
- 更新 `raw/index.md`
- 写入 `wiki/log.md`

这里的关键约束是：

**raw 的迁移必须由用户确认触发。**

### 5. Query

日常提问时运行 `query_wiki`，优先基于现有 wiki 页面回答，而不是直接绕过 wiki 回到 raw。

### 6. Inspool

如果一次问答很有价值，就运行 `inspool`，把结果回填为新的 wiki 页面，通常优先进入 `wiki/synthesis/`。

### 7. Lint

定期运行 `lint_wiki`，检查：

- 孤立页面
- 旧路径残留
- 缺少来源支撑的强结论
- 关系字段缺失
- 用户分类迁移后的链接问题
- `raw/index.md` 与实际目录状态是否一致

## 使用规范

为了让这个项目长期可维护，建议遵守这些规则：

- raw 是事实来源层，不要随意改写正文
- wiki 是知识综合层，允许持续重写和演化
- 关键结论优先链接本地 `sources` 页面，而不是直接贴外部 URL
- 不确定的内容写成"待确认"或"开放问题"
- 当新资料与旧结论冲突时，要显式记录，不要静默覆盖
- 用户可以手动给 `concepts / entities / synthesis` 建子文件夹分类，代理应兼容这种组织方式
- 页面链接优先使用 Obsidian 双链，不要把深层路径写死为知识语义

## Ingest 单元

默认处理单位不是固定"一篇"，而是一个 ingest 单元。

一个 ingest 单元可以是：

- 一篇独立素材
- 一组明确相关的小批量素材

判定优先级：

1. `raw/unprocessed/` 下同一子文件夹
2. 相同 `ingest_group`
3. 用户明确说明需要一起处理
4. 否则按单篇处理

建议每批次控制在 2 到 5 篇。

## 图谱思路

这个项目希望在 Obsidian 中形成可浏览的知识关系图谱。

推荐分层：

- `raw/`：原始资料节点
- `wiki/sources/`：证据节点
- `wiki/entities/`：实体节点
- `wiki/concepts/`：概念节点
- `wiki/synthesis/`：综合判断节点

关键原则：

- 外部 URL 不是主图谱边
- 主关系应当连接本地 markdown 页面
- `sources` 是证据锚点
- `supports / contradicts / related_*` 用于 frontmatter 结构化关系
- 正文双链用于阅读和 Graph 展示

## 命令与技能

Codex skill：

- [ingest_raw](./.codex/skills/ingest_raw/SKILL.md)
- [approve_ingest](./.codex/skills/approve_ingest/SKILL.md)
- [query_wiki](./.codex/skills/query_wiki/SKILL.md)
- [inspool](./.codex/skills/inspool/SKILL.md)
- [lint_wiki](./.codex/skills/lint_wiki/SKILL.md)

Claude Code 命令：

- [ingest_raw](./.claude/commands/ingest_raw.md)
- [approve_ingest](./.claude/commands/approve_ingest.md)
- [query_wiki](./.claude/commands/query_wiki.md)
- [inspool](./.claude/commands/inspool.md)
- [lint_wiki](./.claude/commands/lint_wiki.md)

通用 Obsidian skill（来自 Obsidian 官方）：

- [defuddle](./.codex/skills/defuddle/SKILL.md)
- [json-canvas](./.codex/skills/json-canvas/SKILL.md)
- [obsidian-bases](./.codex/skills/obsidian-bases/SKILL.md)
- [obsidian-cli](./.codex/skills/obsidian-cli/SKILL.md)
- [obsidian-markdown](./.codex/skills/obsidian-markdown/SKILL.md)

## Codex 与 Claude 的使用方式

- Codex 用户使用 [AGENTS.md](./inspool-wiki-zh/AGENTS.md)，配合 `.codex/skills/` 下的 skill。
- Claude 用户使用 [CLAUDE.md](./inspool-wiki-zh/CLAUDE.md)，配合 `.claude/commands/` 下的命令。

## 快速开始

1. 将 `inspool-wiki-zh/` 复制到你的 Obsidian Vault
2. 配置 Obsidian Web Clipper 保存到 `inspool-wiki-zh/raw/unprocessed/`
3. 让你的代理读取规则文件：
   - Codex: `inspool-wiki-zh/AGENTS.md`
   - Claude: `inspool-wiki-zh/CLAUDE.md`
4. 剪藏第一篇文章
5. 运行 `ingest_raw`
6. 审阅后运行 `approve_ingest`
7. 后续持续用 `query_wiki`、`inspool` 和 `lint_wiki` 维护这个 wiki

## 推荐 Obsidian 插件

| 插件 | 作用 |
|------|------|
| **Claudian** | 在 Obsidian 内调用 Claude，配合本项目命令直接在编辑器中完成 ingest、query 等操作 |
| **Templater** | 模板引擎，用于快速创建符合本项目结构约定的来源页、实体页、概念页等 |
| **Dataview** | 基于 frontmatter 字段生成动态查询视图，方便浏览 `supports`、`contradicts` 等关系 |
| **Outliner** | 增强列表和大纲体验，适合 wiki 页面的层级结构和折叠浏览 |
| **Another Quick Switcher** | 增强快速跳转，支持模糊搜索和双链跳转，提高 wiki 页面间导航效率 |
| **File Explorer Note Count** | 在文件管理器中显示每个文件夹下的笔记数量，直观掌握 `sources/`、`entities/` 等目录的积累情况 |
| **Custom Attachment Location** | 自定义附件保存路径，配合本项目把图片统一存到 `raw/assets/` |

## 适合继续扩展的方向

- 增加更细的 graph 关系约束
- 增加领域化页面模板
- 针对 Claude / Codex 继续优化各自的命令封装
