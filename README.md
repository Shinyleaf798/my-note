# tech-note

我的技术笔记。push 到 main 后，会自动发布到 [笔记站](https://shinyleaf798.github.io/my-portfolio/)。

## 文件夹

| 文件夹 | 放什么 | 会发布吗 |
| --- | --- | --- |
| `notes/` | 笔记，换个项目也读得懂 | ✅ |
| `news/` | 看到的资讯 + 自己的理解 | ✅ |
| `project/<名字>/` | 项目介绍页：只放 `index.md` 和图片 | ✅ |
| `interview/` | 面试复习 | ❌ |

两个 `example.md` 是格式参考，带 `draft: true` 所以不发布。
**`news/example.md` 不要删**：`news/` 空了，网站会部署失败。

## 写一篇笔记

1. **放哪**：都放 `notes/`，不建子文件夹。
   - 在个人项目里学到的：加 `type: project` + `relatedProject: "corddaily"`，会出现在项目页。
   - 只对公司项目成立的：不放这里。
2. **文件名**：`YYYY-MM-DD-英文短名.md`，小写、用 `-` 连接。建好后文件名和 `date` 都不改。
3. **新写还是改旧的**：新内容能单独回答一个问题 → 新建，用 `relatedNotes` 指向旧的
   （两篇都会显示对方，不用改旧笔记）。只是修正、补充 → 直接改旧的。拿不准就新建。
4. **正文**：一篇只讲一件事。不要写 `[xx](other.md)` 链接，网站上会打不开。
5. **没写完**：加 `draft: true` 先 push，写完删掉这行才会发布。

```markdown
---
title: "标题"
date: 2026-11-02
description: "一句话说明这篇讲什么"
frameworks: ["React"]
tools: ["Docker"]
domains: ["前端"]
tags: ["除错"]
relatedNotes: ["2026-10-25-xxx"]   # 有关联才写
---

## 二级标题会出现在右边的目录
```

## 项目介绍页

`project/<名字>/index.md`，写给**不懂代码的人**看：

1. 为什么做这个项目
2. 做出来的样子（截图，每张配一句说明）
3. 能做什么
4. 用了什么技术（一张表就好）

图片和 `index.md` 放同一个文件夹，用 `![说明](./home.jpg)` 引用。
想在 Projects 列表显示截图，在 frontmatter 加 `cover: ./cover.png`（App 截图再加 `coverFrame: phone`）。
怎么安装、怎么跑代码，写在项目自己仓库的 README，不写这里。

## 字段

**必填**：`title`、`date`、`description`。资讯还要 `sourceUrl`（完整网址）。

**选填**（不用的整行不写）：

| 字段 | 填什么 | 现有的值 |
| --- | --- | --- |
| `languages` | 编程语言 | TypeScript、JavaScript、Rust |
| `frameworks` | 框架 & 库 | React、React Native、Next.js、Expo、Express、GraphQL、Relay、Prisma、next-intl、Astro、Kysely |
| `tools` | 工具 & 平台 | pnpm、npm、Git、GitHub Actions、CI/CD、Docker、CMake、Gradle、Stripe CLI、GraphQL Code Generator、Claude Code、CLI、SQLite、Neon |
| `domains` | 领域 | 前端、后端、移动端、DevOps、AI辅助开发、AI工程 |
| `tags` | 笔记类型 + 关键词 | 类型：`概念` `除错` `踩坑` `流程` `面试` `资讯`<br>关键词：`依赖管理` `学习方法` `构建` `类型系统` `测试` `Git协作` `本地优先` `离线同步` |
| `relatedNotes` | 相关笔记的文件名（不带 `.md`） | |
| `draft` | `true` = 不发布 | |
| `featured` | 项目页用，`true` = 放首页，并在 Projects 页显示成大卡片 | |
| `repoUrl` | 项目页用，GitHub 网址 | |
| `cover` | 项目页用，封面截图，和 `index.md` 同文件夹：`./cover.png` | |
| `coverFrame` | 项目页用，封面外框：`phone` = 手机，`browser` = 浏览器；不写 = 普通图片 | `phone`、`browser` |

- 上面几栏的值会直接变成网站上的筛选按钮，先从现有的里挑，新增了回来补。
- 只有一篇会用到的词不做标签。技术名放前四栏，不放 `tags`。

## 写错了会怎样

| 情况 | 结果 |
| --- | --- |
| 少了必填字段、日期写错 | 这篇不发布，其他照常，GitHub Actions 会显示红叉 |
| `cover` 图片文件名对不上 | 这个项目不发布，其他照常，GitHub Actions 会显示红叉 |
| 正文里的图片文件名对不上 | **整个网站都更新不了** |
| `sourceUrl` / `repoUrl` 不是完整网址 | **整个网站都更新不了** |
| `relatedNotes` 文件名写错 | 不报错，只是连不上 |

push 之后去 GitHub 的 Actions 页面看一眼有没有红叉。
