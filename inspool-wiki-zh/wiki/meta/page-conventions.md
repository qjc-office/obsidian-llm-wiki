# 页面规范

本页总结 `inspool-wiki-zh/wiki/` 中页面的推荐写法。

## 通用要求

- 默认全部使用中文；除非用户明确要求其他语言
- 标题清楚
- 段落简洁
- 尽量使用 `[[双链]]`
- 重要结论尽量附带来源页
- 不确定内容要标记出来
- 关键证据优先链接到本地 `sources` 页面，而不是直接贴外部 URL

## 图谱友好写法

为了在 Obsidian 中形成更清晰的关系图谱，建议同时维护：

1. 正文里的显式双链
2. frontmatter 里的结构化关系字段

正文用于阅读和 Graph 展示，frontmatter 用于 Dataview 查询和后续自动化。

## 用户手动分类

用户可能会在以下目录下手动新建子文件夹做分类：

- `wiki/concepts/`
- `wiki/entities/`
- `wiki/synthesis/`

这类子文件夹是组织层，不是知识关系本身。

因此建议：

- 页面关系依然主要靠 `[[双链]]` 和 frontmatter 关系字段维护
- 不要在正文里把某个子文件夹路径写成知识事实
- Dataview 查询尽量查询顶层类型目录，而不是过度依赖某个固定子文件夹
- 页面迁移后如果需要分类展示，优先更新索引页，不要重写大量正文路径

## 推荐 frontmatter

如果后续你想配合 Dataview 使用，可以逐步加入 YAML frontmatter，例如：

```yaml
---
type: source
status: active
topic: ai-agents
updated: 2026-04-11
---
```

如果要做关系图谱，建议尽早使用更完整的 frontmatter。

### 来源页 frontmatter 示例

```yaml
---
type: source
status: active
topic: ai-agents
updated: 2026-04-11
raw_note: "[[inspool-wiki-zh/raw/unprocessed/某篇原文]]"
external_url: https://example.com/article
related_entities:
  - "[[entities/某个实体]]"
related_concepts:
  - "[[concepts/LLM Wiki]]"
related_sources: []
---
```

### 实体页 / 概念页 / 综合页 frontmatter 示例

```yaml
---
type: concept
status: active
topic: ai-agents
updated: 2026-04-11
supports:
  - "[[sources/某篇来源]]"
contradicts: []
related_entities:
  - "[[entities/某个实体]]"
related_concepts:
  - "[[concepts/Agent Workflow]]"
related_sources:
  - "[[sources/另一篇来源]]"
related_synthesis: []
---
```

`supports` 和 `contradicts` 是最重要的两个字段，优先维护。

`raw_note` 建议使用 vault 内可解析的 wiki 链接，优先写完整路径，并始终保持为 raw 文件的当前真实位置，例如：

`"[[inspool-wiki-zh/raw/unprocessed/某篇原文]]"`

补充约定：

- ingest 阶段，`raw_note` 通常会先指向 `raw/unprocessed/`
- approve 迁移后，必须同步改为 `raw/processed/`
- 如果正文里写了“原始资料”链接，也必须和 `raw_note` 一起更新

## 来源页模板建议

```md
---
type: source
status: active
topic:
updated:
raw_note:
external_url:
related_entities: []
related_concepts: []
related_sources: []
---

# 标题

## 来源信息

## 核心摘要

## 关键观点

关键结论尽量在句尾追加来源页内锚点，例如：

- 某个关键判断。见 [[sources/本页标题#关键观点]]

## 证据摘录

## 可抽取实体

## 可抽取概念

## 与现有 wiki 的连接

## 待确认问题

## 相关来源
```

## 实体页模板建议

```md
---
type: entity
status: active
topic:
updated:
supports: []
contradicts: []
related_entities: []
related_concepts: []
related_sources: []
related_synthesis: []
---

# 实体名

## 一句话定义

## 背景

## 关键属性

## 支持证据

- [[sources/某篇来源#关键观点]]

## 分歧与冲突

## 相关概念

## 相关来源

## 未决问题
```

## 概念页模板建议

```md
---
type: concept
status: active
topic:
updated:
supports: []
contradicts: []
related_entities: []
related_concepts: []
related_sources: []
related_synthesis: []
---

# 概念名

## 定义

## 核心机制

## 与相邻概念的区别

## 支持证据

## 限制或反例

## 相关实体

## 相关来源
```

## 综合页模板建议

```md
---
type: synthesis
status: active
topic:
updated:
supports: []
contradicts: []
related_entities: []
related_concepts: []
related_sources: []
related_synthesis: []
---

# 主题名

## 主题说明

## 结论摘要

## 支持证据

## 分歧与冲突

## 当前判断

## 开放问题

## 后续建议

## 相关来源
```

## 引用规则

- 关键结论优先链接到本地来源页，例如 `[[sources/某篇来源]]`
- 如果可以定位到具体小节，优先使用锚点，例如 `[[sources/某篇来源#关键观点]]`
- 外部 URL 尽量只保留在来源页的“来源信息”中
- 证据不足时，不要伪造 `supports`
- 如果用户迁移了页面分类目录，优先依赖 Obsidian 自动更新的 wiki 链接，不要人工写死旧路径

## Dataview 示例

### 找出没有 supports 的概念页

```dataview
TABLE file.link AS 页面
FROM "inspool-wiki-zh/wiki/concepts"
WHERE !supports OR length(supports) = 0
```

这个查询会自动覆盖 `concepts/` 下的所有子文件夹，适合用户手动分类后的结构。

### 找出存在冲突来源的综合页

```dataview
TABLE file.link AS 页面, contradicts AS 冲突来源
FROM "inspool-wiki-zh/wiki/synthesis"
WHERE contradicts AND length(contradicts) > 0
```
