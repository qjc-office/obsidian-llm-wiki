---
type: meta
status: active
updated: 2026-04-11
related_sources: []
related_synthesis: []
---

# 工作流总览

## 目的

本页用于总结 `inspool-wiki-zh/` 的完整工作流，以及每一步对应的项目内 skill 命令文件，方便后续重复执行而不必重新口头约定。

## 总流程

1. 把原始资料放入 `inspool-wiki-zh/raw/unprocessed/`
2. 如有需要，使用子文件夹或 `ingest_group` 组织成一个 ingest 单元
3. 执行 ingest，把 raw 编译进 wiki，但不直接归档
4. 审阅本次沉淀结果
5. 审阅通过后执行 approve，把 raw 迁移到 `raw/processed/`，并回补 wiki 中的 raw 引用
6. 日常问答优先基于 wiki，而不是直接回到 raw 做一次性总结
7. 将高价值回答回填到 wiki
8. 定期执行 lint，检查结构、证据链、状态机和图谱关系

## ingest 单元

默认处理单位不是固定“一篇”，而是一个 ingest 单元。

一个 ingest 单元可以是：

- 一篇独立 raw 素材
- 一小组明确相关的 raw 素材

判定优先级：

1. `raw/unprocessed/` 下同一子文件夹
2. 相同 `ingest_group`
3. 用户明确说明这几篇需要一起处理
4. 否则按单篇处理

建议单批次控制在 2 到 5 篇。

## 状态机

raw 素材的状态流转如下：

- `unprocessed`
- `pending_review`
- `processed`

说明：

- `ingest` 只会把 raw 推进到 `pending_review`
- `approve` 才会把 raw 推进到 `processed`
- 未经用户确认，不允许直接从 `unprocessed` 进入 `processed`

## 核心操作

### 1. ingest_raw

选择下一个 ingest 单元，创建或更新 `sources / entities / concepts / synthesis`，更新索引与日志，将 raw 标为 `pending_review`。

### 2. approve_ingest

找到最近一组 `pending_review` 的 raw，核对 wiki 页面存在后将 raw 迁移到 `raw/processed/`，同步回补 wiki 中的 raw 路径引用，更新索引与日志。

### 3. query_wiki

优先读取 `wiki/index.md`，基于现有 wiki 页面综合回答，关键判断优先引用本地 `sources` 页面。

### 4. inspool

把一次值得保留的问答沉淀回 wiki（默认优先 `synthesis/`），同步更新索引与日志。

### 5. lint_wiki

检查孤立页面、弱证据、重复页、旧路径残留、关系字段缺失、`pending_review` 积压、图谱中的 `supports / contradicts / related_*`。

## 图谱规则

当前图谱约定如下：

- `raw/`：原始资料层
- `wiki/sources/`：证据节点
- `wiki/entities/` 与 `wiki/concepts/`：知识骨架
- `wiki/synthesis/`：高层判断

关键结论优先使用：

- `[[sources/某篇来源]]`
- `[[sources/某篇来源#某个小节]]`

不推荐让外部 URL 直接充当主要关系边。

## 用户手动分类

用户可能会在以下目录下手动新建子文件夹并迁移页面：

- `wiki/concepts/`
- `wiki/entities/`
- `wiki/synthesis/`

这属于正常维护动作。代理必须默认兼容这些子文件夹，不能把它们当作稳定不变的语义真相。

## 默认语言

除非用户明确要求其他语言，否则：

- wiki 沉淀文档默认使用中文
- 索引、日志、说明页和命令模板默认使用中文
- raw 原始资料可保留原语言

## 建议使用顺序

如果是常规新资料处理，推荐按这个顺序执行：

1. 导入 raw 到 `raw/unprocessed/`
2. 运行 `ingest_raw`
3. 审阅新生成或更新的 wiki 页面
4. 审阅通过后运行 `approve_ingest`
5. 后续问答优先走 `query_wiki`
6. 对高价值问答执行 `inspool`
7. 周期性执行 `lint_wiki`
