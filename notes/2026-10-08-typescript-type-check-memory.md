---
title: "一行型别签章吃掉 1GB——CI build 挂在 exit 137"
date: 2026-10-08
description: "CI build 无声失败、exit code 137、日志里没有编译错误、本机却编得过。原因是参数标成一个巨大的型别,但函式只用到其中一小块,编译器还是把整个型别比了一遍。"
languages: ["TypeScript"]
frameworks: ["Prisma"]
tools: ["Docker", "CI/CD"]
domains: ["后端", "DevOps"]
tags: ["除错", "类型系统", "构建性能", "除错方法"]
---

# 一行型别签章吃掉 1GB——CI build 挂在 exit 137

## 问题

CI 上 build 失败：

```
pnpm run build exited (137)
```

- 日志里**一行编译错误都没有**
- 重跑两次，挂在同一个地方，耗时一样
- **本机编得过**

`137 = 128 + 9` = 进程被 **SIGKILL** 杀掉。不是程式写错，是**被强制中断** —— 记忆体不够，内核挑一个进程杀了。

所以方向一开始就要转：去翻编译错误永远找不到。

## 为什么

### 第 1 步：TypeScript 是「看形状」的

```ts
type Needed = { name: string }

const thing = { name: "abc", age: 5 }

function f(x: Needed) {}
f(thing)   // ✅ 过
```

`thing` 的型别不叫 `Needed`，但它**有 `name: string`**，所以过。

TypeScript 不看名字，只问：**「你身上有我要的每一个东西吗？」** 而这个检查要**逐个属性比对**。

上面只有 1 个属性，瞬间完成。

### 第 2 步：型别一模一样时，根本不用比

```ts
const a: Needed = { name: "abc" }
f(a)   // ✅ 过，而且 0 成本
```

`a` 的型别**就是** `Needed`。编译器一看「同一个型别」，直接通过，**连比都不用比**。

👉 **关键差别：同型别 = 免费；不同型别 = 要逐个比。**

### 第 3 步：如果要比的东西很大呢

`Prisma.TransactionClient` 简化后长这样：

```ts
type TransactionClient = {
  user:   { findMany(...); create(...); update(...) /* 约 20 个方法 */ }
  post:   { findMany(...); create(...); update(...) }
  order:  { findMany(...); create(...); update(...) }
  // ...还有上百个表
}
```

**上百个表 × 每个约 20 个方法**，而且每个方法都是 Prisma 生成的超复杂泛型（生成码几十万行）。

### 第 4 步：问题就在这里

```ts
async function validate(trx: Prisma.TransactionClient) {
  await trx.some_table.findMany(...)   // 只用到这一个表！
}
```

```ts
// 呼叫 A：在 transaction 里
this.prisma.$transaction(async (trx) => {
  await validate(trx)        // ✅ trx 的型别就是 TransactionClient → 免费
})

// 呼叫 B：直接传 service 本体
await validate(this.prisma)  // ⚠️ PrismaService ≠ TransactionClient → 要逐个比
```

呼叫 B 让编译器老老实实跑第 1 步那套流程：

```
PrismaService 有 user 吗？→ 有，形状一样吗？→ 展开 20 个方法逐个比...
PrismaService 有 post 吗？→ 有，展开比...
PrismaService 有 order 吗？→ 有，展开比...
... × 上百次
```

**这一趟要 1GB 记忆体。** 我写了两处，就是 2GB。

> 荒谬的地方：函式**只用到 1 个表**，却因为参数标成完整型别，让编译器把上百个表全比了一遍。

### 第 5 步：为什么本机测不出来

`.dockerignore` 里排除了 `**/tsconfig.tsbuildinfo`（增量编译的进度档）。

所以 **CI 永远是冷编译，本机通常是增量编译**。同一份程式码：

- 增量：**1.1 GB**
- 冷编译：**3.3 GB**

差三倍。本机没事不是运气好，是**测的根本不是同一件事**。

### 第 6 步：为什么日志里什么都没有

两个原因叠在一起：

1. **OOM killer 的讯息写在宿主机的 kernel log**，不会进到 docker build 的输出
2. 被杀的是 `tsc`，但**父进程 `pnpm` 活下来了**，所以你只看到它回报一个 exit code

## 如何解决

用 `Pick<>` 把参数缩到**实际用到的那几个**：

```ts
// 之前：我要「完整的 client」
trx: Prisma.TransactionClient

// 之后：我只要「有 some_table 这个表就好」
trx: Pick<Prisma.TransactionClient, 'some_table'>
```

`Pick<T, 'k'>` = 从 `T` 里只挑出 `k` 这个属性。

编译器的工作变成：

```
PrismaService 有 some_table 吗？→ 有，形状一样 → 通过
（结束）
```

**上百个表 → 1 个表。**

而且两种呼叫方式都还能跑 —— `trx` 和 `this.prisma` **都**有 `some_table`。

实测冷编译峰值：**3,274 MB → 2,337 MB**，省 937 MB。CI 设定一个字都不用改。

## 小总结

**这跟 Prisma 没有必然关系。**

> 参数标成一个巨大的型别，但函式只用到其中一小块 —— 编译器还是会把**整个**型别比一遍。
> 用 `Pick<>` 告诉它「我只需要这几个」，它就只比这几个。

任何「超大型别 + 只用一小部分」都一样，只是 Prisma 生成的型别特别巨大，所以后果特别明显。

**诊断顺序：**
1. 先看 exit code —— `137` 就别找编译错误了，去想资源
2. 确认本机跟 CI 做的是同一件事（有没有增量快取？）
3. 量真实峰值，不要猜
4. 回头找最近有没有新增「巨大型别的结构性比对」
5. **最后**才怀疑 CI 的记忆体设定

**三个想过但错的方向：**
- 把并行的 build 拆开跑 → 治症不治因，还让每次 build 慢一倍
- CI 换大台机器 → 花钱绕过，下次照样炸
- 加 `--max-old-space-size=3072` → **最危险**。对 SIGKILL 来说调大堆是反效果；而这个 3072 比实际需要的 3.3GB 还低，会让 build 每次都挂

**带走三句话：**
- exit code 是第一手线索，不是噪音
- 「本机没问题」要先问「本机跟 CI 做的是同一件事吗」
- **型别检查也会耗资源，一个签章可以差 1GB** —— 参数收最小型别不是洁癖，是真的有成本

最后：我一开始是根据**错的测量值**（增量的 1.1GB）推论的，所以上面三个方向全错。**量错了就会建议错 —— 先量再改。**
