---
title: 'CordDaily — 本地优先的记账 App'
date: 2026-10-21
description: '本地优先的个人记账 App：手机本地 SQLite 是唯一的数据源头，不登录也能记账，云端只是一条可选的备份通道。'
featured: true
repoUrl: 'https://github.com/Shinyleaf798/CordDaily'
languages: ["TypeScript", "JavaScript"]
frameworks: ["React Native", "Expo", "Express", "Prisma"]
domains: ["移动端", "前端", "后端"]
tags: ["记账", "本地优先", "离线同步", "SQLite", "Neon", "Android"]
---

## 这是什么

一个 Android 记账 App。**手机本地的 SQLite 是唯一的数据源头** —— 不注册、不登录、断网都能照常记账，
云端只是一条可选的备份通道。

这个取舍决定了后面几乎所有的设计：id 得在手机上生成，同步只能是单向的，余额不能存在库里。

## 界面

金额、分类、账户，一笔三步录完。内置 22 个一级分类 + 14 个二级分类。

按月、按分类聚合，点进去看明细。

按天回看每笔收支。

填自己的 Neon 连接串，手机直连自己的数据库 —— 数据不经过我的服务器。

## 功能

- 记收支，分类 + 账户两个维度（账户是纯标签，回答「这笔账用什么付的」）
- 按月 / 按分类统计聚合，日历视图按天回看
- 周期性交易：App 打开时按 `nextRunDate` 本地补生成到期的记录，支持很久没打开过的批量补齐
- 预算超支提醒，**纯本地计算**，很久没同步也准
- 导出到文件 / 从文件恢复
- 云端备份与恢复（可选）

## 架构：为什么是「本地优先」

```
手机本地 SQLite ← 唯一的数据源头，离线完全可用
      │
      │ 单向推送（本地 → 云端）
      ▼
   按「用户有没有自己填 Neon 连接串」自动二选一
      │
      ├─ 填了 → 手机直连用户自己的 Neon，App 自己建表，不经过任何服务器
      │          发布版走这条 —— 我拿不到任何人的财务数据
      │
      └─ 没填 → backend（Express + Prisma + JWT 鉴权 + 同步接口 + 统计聚合）
```

两条通道在 `mobile/src/api/cloud-transport.ts` 里按配置选实现，上层调用方不关心走的是哪条。

`backend/` 留着有两个理由：电脑端只读网页以后要走它；那套 Prisma schema 是远端建表 DDL 的对照基准 ——
同一个库既要能被手机直连写、也要能被后端读，所以表名列名必须逐字一样（表名是 `"Transaction"` 不是
`transactions`，列名 camelCase，`DECIMAL(65,30)`）。

## 几个关键决策

这几条不是风格偏好，是会在别处炸的硬约束：

**id 由客户端生成。** 所有主表记录创建时在手机上生成 UUID，Prisma schema 里刻意**不加**
`@default(uuid())` —— 离线创建的记录必须全局唯一，同步重试才能靠 id 做幂等去重。

**同步刻意只做单向。** 云端只在一种情况下往手机送数据：重装后本地库是空的、且用户明确点了「恢复」。
「从云端拉下来合并进一个有数据的库」要配套冲突解决、删除墓碑、版本向量 —— 那是另一个子系统，
不是一个开关。（分类和账户是例外，它们确实做了三方合并。）

**余额永远现算，不在库里存。** `openingBalance` + 交易汇总当场算出来，绝不存一个会被更新的
`balance` 字段 —— 离线同步一定会让它跟实际对不上。

**导出文件和云端 bundle 是同一个 JSON 结构。** 所以「恢复」只有一套代码路径。代价是备份包必须
自解释：交易里出现的任何 id，同一份包里都要找得到，所以分类和账户是备份包的必需组成部分。

**内置分类的 UUID 是写死的常量**，每台设备一样，恢复时备份包里的 `categoryId` 不用改写。
代价是这批 id 在所有用户间共享，所以服务器上 `Category` / `Account` 的主键是 `@@id([userId, id])` 复合键。

**Android 包从约 116 MB 压到约 67 MB。** 把 `reactNativeArchitectures` 锁成 ARM-only —— 默认那四套里
x86/x86_64 占了约 50 MB，而只有模拟器用得上，市面上没有 x86 的 Android 手机。这个约定写成了
config plugin（`mobile/plugins/with-release-signing.js`），而不是手改 `android/` —— 后者会被下次
`expo prebuild` 悄悄抹掉。

## 技术栈

| 层 | 用了什么 |
| --- | --- |
| App | React Native + Expo（SDK 57）、Expo Router、TypeScript |
| 本地存储 | SQLite（唯一数据源头） |
| 状态 | React Query 取数 + zustand 管登录态 / 首页布局 / 主题 |
| 云端（可选） | Neon Postgres，手机直连 或 Express + Prisma + JWT |
| 打包 | Gradle + Expo config plugin（签名、ARM-only） |

## 现状

- **`mobile/`** —— 项目主体，在用，能打出签名的 release APK
- **`backend/`** —— 在用，但不是发布版的默认通道
- **`web/`** —— 电脑端只读看账 + 图表（Next.js），还没开始
