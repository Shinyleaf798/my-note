# tech-note

个人笔记仓库,用于记录技术资讯与学习笔记。

## 目录结构

- [news/](news/) — 资讯类笔记,记录看到的文章、新闻等内容及个人理解
- [notes/](notes/) — **通用笔记**,换个项目也成立的知识
- [interview/](interview/) — 面试复习整理。**不参与站点构建**,只在仓库里留存(见 `.github/workflows/notify-portfolio.yml` 的 paths 过滤)
- [project/](project/) — 个人项目,一个项目一个目录;项目页 + 该项目专属的笔记

`news/` 与 `notes/` 下的 `example.md` 为格式参考,可在实际使用时删除。

## 一篇笔记该放哪

先问一句:**换个项目还会遇到吗?**

| 情况 | 放哪 | 怎么写 |
| --- | --- | --- |
| 会遇到 | `notes/` | 泛化,脱离项目也读得懂;可加 `relatedProject` 标出处 |
| 只对某个个人项目成立 | `project/<项目>/` | 随便写具体,包名脚本名都行 |
| 只对公司项目成立 | **不进这个仓库** | 留在公司仓库的 `DECISIONS.md` |

`notes/` 里**不分子目录**,靠分面字段和标签区分。技术笔记常常跨多个领域
(一篇既是 GraphQL 又是除错又是前端),分目录要纠结"放哪一个",字段可以同时挂上。

## 文件命名

`YYYY-MM-DD-英文短名.md`,全小写、连字符分词。例:`2026-10-08-graphql-relay-error.md`。

## 文件格式

笔记均为 Markdown,文件头部用 frontmatter 描述元信息。日期统一 `YYYY-MM-DD`。
`project/` 下的笔记用和 `notes/` 完全相同的字段,这样它们一样能进筛选面板。
`interview/` 下的也照这个格式写,但它不参与站点构建,字段目前不起作用 ——
留着是为了将来想发布时直接改 workflow 的 paths 就行。

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `title` | ✅ | 标题 |
| `date` | ✅ | 日期 |
| `description` | ✅ | 一句话说明这篇笔记讲了什么 |
| `tags` | ✅ | 普通标签,见下方「标签」 |
| `languages` | | 编程语言,见下方「分面字段」 |
| `frameworks` | | 框架 & 库 |
| `tools` | | 工具 & 平台 |
| `domains` | | 领域 |
| `relatedProject` | | 仅 `notes/` 用,见下方「项目归属」 |
| `sourceUrl` | 仅 news/ | 原文链接 |

## 项目归属

两种关系,不要混:

- **`project/corddaily/xxx.md`** — 这是 corddaily 的笔记。归属由**目录**决定,不写字段。
- **`notes/xxx.md` + `relatedProject: "corddaily"`** — 这是通用知识,只是在 corddaily 里
  学到的。笔记本身必须脱离项目也读得懂。

项目页面可以据此分两栏列出:「项目笔记」(扫目录) 和「由本项目产出的通用笔记」(扫字段)。

> **公司项目来源的笔记永远不填 `relatedProject`。** 没有出处可写不是遗漏,是对的 ——
> 那个项目不存在于这个仓库,也不该存在。

## 分面字段

`languages` / `frameworks` / `tools` / `domains` 对应笔记站筛选面板里的四组,
每组独立筛选。不适用的字段**整行省略**,不要留空数组。

值用技术的正式写法(`TypeScript`、`Next.js`、`React Native`、`Stripe CLI`),
因为它会直接当成筛选按钮上的文字显示。同一个词只能出现在一个字段里,
不要既放 `domains` 又放 `tags`,否则筛出来的结果对不上。

| 字段 | 面板标题 | 现有值 |
| --- | --- | --- |
| `languages` | 编程语言 | TypeScript、JavaScript、Rust |
| `frameworks` | 框架 & 库 | React、React Native、Next.js、Expo、GraphQL、Relay、Prisma、next-intl、Astro |
| `tools` | 工具 & 平台 | pnpm、npm、Git、GitHub Actions、CI/CD、Docker、CMake、Gradle、Stripe CLI、GraphQL Code Generator、Claude Code、CLI |
| `domains` | 领域 | 前端、后端、移动端、DevOps、AI辅助开发 |

## 标签

`tags` 放两类东西:**这是哪种笔记**,以及细碎的关键词。

笔记类型建议从这几个里选一个:`概念`(解释某个东西是什么)、`除错`(一次具体问题的
定位过程)、`踩坑`(结论型的坑与避免方法,不含完整排查过程)、`流程`(可重复执行的
步骤)、`面试`、`资讯`(news/ 用)。

关键词现有:`API设计`、`Git协作`、`Hook`、`codegen`、`i18n`、`webhook`、`供应链安全`、
`依赖管理`、`学习方法`、`本地开发`、`构建性能`、`构建配置`、`测试`、`类型系统`、
`笔记方法`、`认证授权`、`错误处理`、`除错方法`

新建标签前先在这里找有没有同义的,优先复用。标签系统唯一的崩坏方式是同义词泛滥
(`除错` / `debug` / `踩坑` / `Debugging` 并存)。新增了记得回来补这张表。

## 写公司项目来源的笔记之前

这个仓库是公开的,公司代码不能外流。从公司项目学到的东西**可以**写,但必须先剥干净。

**红线 —— 一个字都不能出现:**

- 公司名、产品名、客户名
- 内部 npm scope、仓库名、包名(`@acme/graphql` → `<graphql-pkg>`)
- 内部服务名、域名、数据库名
- 业务实体名(`EventRefundStatus`、`tenant_subscription` → 换成 `Item`、`Order` 这类通名)
- package.json 里的自定义 script 名(`relay-web:dev` → `<relay-watch>`)

**需要判断的 —— 规模指纹:**

表数量、代码行数、员工数这类数字会暴露系统规模。问一句"这个数字是论证需要,还是只是
顺手写的"。`exit 137 是被 SIGKILL` 是论证;`165 个表` 换成 `上百个表` 不损失任何东西。

**不受此限:** 个人项目(`project/` 下的)随便写具体,那是你自己的。

剥完之后再读一遍:**这篇笔记现在还能读懂吗?** 读不懂说明泛化过头,知识点本身没留下。

### 自动检查

`.githooks/pre-commit` 会扫描每次提交的新增内容,命中禁用词就拦截。启用:

```bash
git config core.hooksPath .githooks
cp .githooks/blocklist.example.txt .githooks/blocklist.txt   # 然后填入实际的词
```

`blocklist.txt` **不进版本库**(它本身才是最敏感的文件),所以:

- 换机器 clone 下来后词表是空的,hook 会打印"跳过检查"然后**静默放行** —— 记得重新建
- 词表自己找个安全的地方备份(密码管理器的安全备注之类)
- 这道检查只防手滑,不防"忘了有这回事";真正的防线是写之前就按上面的红线剥干净
