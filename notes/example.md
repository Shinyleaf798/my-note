---
title: "示例笔记 (格式参考, 可删除)"
date: 2026-01-01
description: "这是一篇示例笔记, 仅用于展示 frontmatter 字段格式。"
relatedProject: "corddaily"
languages: ["TypeScript"]
frameworks: ["React"]
tools: ["pnpm"]
domains: ["前端"]
tags: ["概念", "示例"]
---

## 这是一个二级标题

必填字段:`title`、`date`、`description`、`tags`。

`relatedProject` 可选, 写在**通用笔记**上, 表示"这个通用问题是在那个项目里遇到的"。
笔记本身要能脱离项目读懂。公司项目来源的笔记**永远不填**这个字段。
项目专属的笔记不用填 —— 它放在 `project/<项目>/` 下, 归属由目录决定。

`languages` / `frameworks` / `tools` / `domains` 是给笔记站做筛选用的分面字段,
每个字段对应筛选面板里的一组, 不适用的直接省略整行。值用技术的正式写法
(`TypeScript`、`Next.js`、`React Native`), 这样直接当筛选按钮上的文字。

`tags` 是普通标签, 放"这是哪种笔记"和细碎关键词, 例如 `踩坑`、`除错`、`流程`、
`错误处理`。新标签请先查 README 的标签表, 优先复用已有的。

### 三级标题示例

正文支持普通 Markdown 语法、代码块、列表等。
