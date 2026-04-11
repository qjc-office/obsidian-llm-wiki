# 原始资料区说明

本目录用于存放未经 LLM 改写的原始资料，以及少量流程性元数据。

## 规则

- `unprocessed/`：新导入、待处理的资料
- `processed/`：已完成 ingest 的资料
- `assets/`：来源页面的图片、附件、截图等
- `index.md`：素材处理状态索引

## 使用建议

- 网页剪藏后先放进 `unprocessed/`
- 如果文章中的图片有价值，尽量本地化到 `assets/`
- 原始资料尽量保留来源标题、原始链接、日期等上下文
- ingest 完成后先进入 `pending_review`
- 只有用户确认没问题后，代理才迁移到 `processed/`

## 注意

这个目录是事实来源层。除非用户明确要求，否则代理不应改写这里的正文内容。

允许的流程性更新：

- 给 frontmatter 增加或更新 `processing_status`
- 写入 `reviewed_at`
- 写入 `processed_at`
- 写入 `processed_into`
- 可选写入 `ingest_group`
- 在 `unprocessed/` 与 `processed/` 之间迁移文件
- 更新 `index.md`

## 批量处理建议

如果多篇 raw 素材应当一起 ingest，推荐使用下面任一方式：

- 把它们放进 `unprocessed/` 下同一个子文件夹
- 给它们加上相同的 `ingest_group`

代理在无参 ingest 时，会优先把这些素材视为同一批次。
