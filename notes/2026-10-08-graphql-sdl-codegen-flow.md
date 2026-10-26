---
title: "GraphQL SDL 改动后的更新流程"
date: 2026-10-08
description: "改了 SDL 之后要重新生成聚合 schema、TS 类型与 Relay 文件,否则前端用的还是落后的产物;附手动命令与 watcher 做法。"
languages: ["TypeScript"]
frameworks: ["GraphQL", "Relay"]
tools: ["pnpm", "GraphQL Code Generator"]
domains: ["前端", "后端"]
tags: ["流程", "构建"]
---

## 为什么会报错

改了后端的 SDL(`.graphql` 文件),但前端依赖的**两样东西没跟着更新**:

1. **聚合 schema(`schema.graphql`)** —— Relay 编译前端 mutation 时对着它校验。里面没有新加的 mutation → Relay 认不出来 → 生成文件不更新 → 报 `xxx does not exist`。
2. **生成的 TS 类型(dist)** —— 前端 import 的类型来源。没更新 → 新加的字段、新的 enum 值在类型里都不存在。

因为 SDL 是"源",而前端用的是"从 SDL 生成的产物"(schema.graphql + types + Relay `__generated__` 文件),**改了源不重新生成,产物就落后**。

## 更新用什么命令

下面用 `<graphql-pkg>` 表示存放 SDL 与生成产物的那个包,`<frontend>` 表示前端包。

### 手动一次性更新(从仓库根目录)

```bash
# ① 重新聚合 schema.graphql + 生成 types
pnpm --filter <graphql-pkg> exec graphql-codegen

# ② 重建 dist(前端 import 类型时拿到的是 dist)
pnpm --filter <graphql-pkg> exec tsc

# ③ 重新编译前端 Relay 生成文件
pnpm --filter <graphql-pkg> exec relay-compiler -p <frontend> --repersist
```

### 开发时推荐:挂 watcher,改 SDL 自动更新

多数仓库会把上面几步包成 script(名字各项目不同,去 `package.json` 里找):

```bash
# codegen + tsc 一起 watch(schema.graphql / types / dist 自动同步)
pnpm --filter <graphql-pkg> <codegen-watch>

# Relay 也 watch(前端生成文件自动重编译)
pnpm --filter <graphql-pkg> <relay-watch>
```

## 记忆口诀

改了 **SDL** 之后要更新的是:

- **`graphql-codegen`** → 聚合 schema + types(给 Relay 校验 + 给 TS 类型)
- **`tsc`** → 把 types 编译进 dist(前端真正 import 的是 dist)
- **`relay-compiler`** → 前端 `__generated__` 文件(mutation/fragment 的变量、类型)

## 小结

- 症状:**SDL 改了,但 schema 校验和 Relay 生成文件都没更新** → 前端报字段/类型不存在
- 更新:`graphql-codegen` + `tsc` + `relay-compiler`(手动),或挂两个 watcher(自动)
- 最容易踩的一种:**只挂了 Relay 的 watcher、没挂 codegen 那半**。SDL 改了,schema 校验用的还是旧的,前端照样报错
