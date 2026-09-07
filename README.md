# tech-note

个人笔记仓库,用于记录技术资讯与学习笔记。

## 目录结构

- [news/](news/) — 资讯类笔记,记录看到的文章、新闻等内容及个人理解
- [notes/](notes/) — 常规笔记,记录学习、项目相关的知识整理

各文件夹下的 `example.md` 为格式参考,展示了对应的 frontmatter 字段,可在实际使用时删除。

## 文件格式

笔记均为 Markdown 文件,文件头部使用 frontmatter 描述元信息。

### news/ 字段说明

| 字段 | 说明 |
| --- | --- |
| `title` | 标题 |
| `date` | 日期 |
| `sourceUrl` | 原文链接 |
| `summary` | 摘要 |
| `tags` | 标签列表 |

### notes/ 字段说明

| 字段 | 说明 |
| --- | --- |
| `title` | 标题 |
| `date` | 日期 |
| `type` | 笔记类型,如 `general`、`project` |
| `category` | 分类 |
| `tags` | 标签列表 |
| `summary` | 摘要 |
| `relatedProject` | 当 `type` 为 `project` 时需填写,值为对应项目文件名(不含 `.md`) |
