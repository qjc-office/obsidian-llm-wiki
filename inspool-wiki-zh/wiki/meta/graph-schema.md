# 图谱方案

本页定义 `inspool-wiki-zh/` 的知识关系图谱方案，目标是在 Obsidian 中同时兼顾：

- 可读的页面结构
- 清晰的 Graph 视图
- 可查询的 Dataview 关系
- 可追溯的证据链

## 核心原则

不要把外部 URL 当作图谱中的主要关系边。

图谱中的主要边应当连接本地 markdown 节点。推荐结构如下：

- `raw/`：原始资料节点
- `wiki/sources/`：证据节点
- `wiki/entities/`：实体节点
- `wiki/concepts/`：概念节点
- `wiki/synthesis/`：综合判断节点

## 为什么 `sources` 是证据节点

外部 URL 适合作为出处记录，不适合作为 Obsidian 图谱中的主边。

更好的做法是：

1. raw 文件保留外部 URL 和原文
2. `wiki/sources/` 生成来源摘要页
3. 其他知识页通过 `[[sources/...]]` 或 `[[sources/...#某小节]]` 挂接证据

这样可以同时得到：

- 可点击的本地证据节点
- 更稳定的图谱结构
- 更好的页面聚合能力

## 推荐关系类型

### 证据边

- `supports`：支持当前页面关键结论的来源
- `contradicts`：与当前页面关键结论冲突的来源

### 主题边

- `related_entities`
- `related_concepts`
- `related_sources`
- `related_synthesis`

这些关系字段推荐写在 frontmatter 中，同时在正文中用双链显式体现。

## 推荐落链方式

### 页级证据链接

适合 frontmatter：

```yaml
supports:
  - "[[sources/某篇来源]]"
```

### 段落级证据链接

适合正文：

```md
某个关键判断。见 [[sources/某篇来源#关键观点]]。
```

页级链接便于结构化查询，段落级链接便于人工核查。

## 建页建议

### 来源页

主要职责：

- 总结单个来源
- 回链 raw 原文
- 抽取实体、概念和相关来源
- 作为其他知识页的证据节点

### 实体页 / 概念页

主要职责：

- 形成主题骨架
- 维护 `supports` 和 `contradicts`
- 与相邻概念和实体建立关系

### 综合页

主要职责：

- 沉淀跨来源的结论
- 显式展示支持证据和冲突证据
- 挂接相关实体、概念和来源

## 最小可用规则

如果不想一开始维护太多字段，至少保证：

1. 所有综合页都有 `supports`
2. 所有关键概念页都有至少一个来源页链接
3. 关键结论在正文里能落到 `[[sources/...#某节]]`

## 反模式

以下写法不推荐：

- 在知识页里直接堆大量裸露 URL
- 只在正文写结论，不给任何本地来源链接
- 只在 frontmatter 写关系，但正文没有任何显式双链
- 把每个微小 claim 都拆成独立页面，导致图谱碎裂

## Dataview 查询示例

### 找出没有证据支持的综合页

```dataview
TABLE file.link AS 页面
FROM "inspool-wiki-zh/wiki/synthesis"
WHERE !supports OR length(supports) = 0
```

### 找出没有关联来源的实体页

```dataview
TABLE file.link AS 页面
FROM "inspool-wiki-zh/wiki/entities"
WHERE !related_sources OR length(related_sources) = 0
```

### 找出存在冲突来源的页面

```dataview
TABLE file.link AS 页面, contradicts AS 冲突来源
FROM "inspool-wiki-zh/wiki"
WHERE contradicts AND length(contradicts) > 0
```
