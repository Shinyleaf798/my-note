# tech-note

个人技术笔记,push 后自动发布到 [笔记站](https://shinyleaf798.github.io/my-portfolio/)。
字段、模板、写错会怎样:见 [docs/publishing-rules.md](docs/publishing-rules.md)。

## 目录

| 目录 | 放什么 | 发布 |
| --- | --- | --- |
| `notes/` | 笔记,换个项目也读得懂 | ✅ |
| `news/` | 看到的资讯 + 自己的理解 | ✅ |
| `project/<名字>/` | 项目页:只放 `index.md` 和图片 | ✅ |
| `interview/` | 面试复习 | ❌ |
| `docs/` | 本仓库的说明文档 | ❌ |

两个 `example.md` 是格式参考,带 `draft: true` 不发布。`news/example.md` 不要删,
`news/` 空了站点会部署失败。

## 写一篇笔记

1. **放哪**:都放 `notes/`,不建子文件夹。
   - 在个人项目里学到的:加 `type: project` + `relatedProject: "corddaily"`,会挂到项目页。
   - 只对公司项目成立的:**不进这个仓库**。
2. **文件名**:`YYYY-MM-DD-英文短名.md`,小写、连字符。建好后文件名和 `date` 都不再改。
3. **新写还是改旧的**:新内容能单独回答一个问题 → 新建,用 `relatedNotes` 指向旧的
   (双向,不用改旧笔记)。只是修正、补充 → 直接改旧的。拿不准就新建。
4. **正文**:一篇只回答一个问题。不要写 `[xx](other.md)` 链接,站点上会 404。
5. 没写完可以加 `draft: true` 先 push。

## 字段值

值用正式写法,会直接显示成筛选按钮。先从下面挑,新增了回来补。不适用的字段整行不写。

| 字段 | 现有值 |
| --- | --- |
| `languages` | TypeScript、JavaScript、Rust |
| `frameworks` | React、React Native、Next.js、Expo、GraphQL、Relay、Prisma、next-intl、Astro、Kysely |
| `tools` | pnpm、npm、Git、GitHub Actions、CI/CD、Docker、CMake、Gradle、Stripe CLI、GraphQL Code Generator、Claude Code、CLI、SQLite、Neon |
| `domains` | 前端、后端、移动端、DevOps、AI辅助开发、AI工程 |

**`tags`** = 一个笔记类型 + 少量关键词。

- 类型:`概念`、`除错`(排查过程)、`踩坑`(结论)、`流程`、`面试`、`资讯`
- 关键词:`依赖管理`、`学习方法`、`构建`、`类型系统`、`测试`、`Git协作`、`本地优先`、`离线同步`

只有一篇会用的词不做标签;技术名放上面的字段,不放 `tags`。新增前先找同义词。
