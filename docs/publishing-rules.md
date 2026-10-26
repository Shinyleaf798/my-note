# 笔记站的发布规则

> 本仓库的内容怎么变成网站 (<https://shinyleaf798.github.io/my-portfolio/>)。
> 站点代码在 `Shinyleaf798/my-portfolio`,字段以它的 `src/content.config.ts` 为准。
> 最后核对:2026-10-26。

## 1. 发布流程

```
push 到 main (改了 notes/ news/ project/)
  → notify-portfolio.yml 通知站点
  → 站点拷贝这三个文件夹 → 检查 frontmatter → 构建 → 上线
```

本仓库不用构建,写好 markdown push 就行。`interview/`、`docs/` 不会发布。

## 2. 文件夹与网址

| 文件夹 | 网址 |
| --- | --- |
| `notes/xxx.md` | `/notes/xxx` |
| `news/xxx.md` | `/news/xxx` |
| `project/xxx/index.md` | `/projects/xxx` |

- **slug** = 文件名去掉 `.md`(项目是文件夹名)。`relatedNotes` / `relatedProject` 填的就是它。
- `notes/` 不建子文件夹,子文件夹会进网址。
- `project/xxx/` 里只放 `index.md` 和图片。多一个 md 就多一个项目页。

## 3. 字段

| 字段 | 必填 | 用在 | 说明 |
| --- | --- | --- | --- |
| `title` | ✅ | 全部 | |
| `date` | ✅ | 全部 | `YYYY-MM-DD`,创建日期,之后不改 |
| `description` | ✅ | 全部 | 一句话摘要 |
| `languages` | | 全部 | 筛选:编程语言 |
| `frameworks` | | 全部 | 筛选:框架 & 库 |
| `tools` | | 全部 | 筛选:工具 & 平台 |
| `domains` | | 全部 | 筛选:领域,也是侧边栏分组 |
| `tags` | | 全部 | 笔记类型 + 少量关键词 |
| `draft` | | 全部 | `true` = 不发布 |
| `relatedNotes` | | notes、news | 相关笔记 slug。notes 是双向的,只在新笔记里写 |
| `type` + `relatedProject` | | notes | `type: project` + 项目 slug,笔记挂到项目页 |
| `sourceUrl` | ✅ | news | 完整网址 `https://...` |
| `featured` | | project | `true` = 首页精选 |
| `repoUrl` | | project | 完整网址 |

不适用的字段整行不写。站点不认识的字段(比如拼错)会被**静默忽略**。

## 4. 模板

**笔记** `notes/YYYY-MM-DD-英文短名.md`

```markdown
---
title: "标题"
date: 2026-10-26
description: "一句话说明这篇讲什么"
frameworks: ["React"]
tools: ["Docker"]
domains: ["前端"]
tags: ["除错"]
relatedNotes: ["2026-10-25-xxx"]   # 有关联才写
---

## 二级标题会进右侧目录
```

**资讯** `news/YYYY-MM-DD-英文短名.md`:同上,再加 `sourceUrl`。

**项目** `project/<slug>/index.md`:同上,可加 `featured`、`repoUrl`。
图片放同一个文件夹,用 `![说明](./home.jpg)` 引用。

## 5. 写错了会怎样

| 情况 | 结果 |
| --- | --- |
| 少必填字段、日期不对、YAML 写错 | 这篇被跳过,其余照常上线,Actions 标红 |
| `sourceUrl` / `repoUrl` 不是完整网址 | **整站构建失败** |
| 图片文件名对不上 | **整站构建失败** |
| slug 拼错 | 不报错,只是关联不上 |
| `notes/`、`news/`、`project/` 变成空的 | **整站构建失败**(所以 `news/example.md` 不要删) |
| 正文里写 `[xx](other.md)` 链接 | 站点上 404。改用 `relatedNotes` |

push 之后去 Actions 页面看一眼有没有红叉。
