---
title: 'GraphQL / Relay:throw Exception 与 "No data returned" 问题'
date: 2026-10-08
description: 'GraphQL 的 throw、null 传播,与 Relay "No data returned" 错误的成因、真实消息藏在哪,以及两种处理方式。'
languages: ["TypeScript"]
frameworks: ["GraphQL", "Relay"]
domains: ["前端", "后端"]
tags: ["除错"]
---

# GraphQL / Relay:throw Exception 与 "No data returned" 问题

## 一、核心背景:GraphQL 响应永远是 `{ data, errors }`

后端无论成功失败,返回的外层结构固定是:

```json
{ "data": ..., "errors": [...] }
```

- `data`:装请求的数据(比如 `Item` 类型,含 id、name 等)
- `errors`:数组,装错误消息

## 二、"No data returned" 到底是什么

它是 **Relay 客户端抛的包装错误,不是后端抛的**。触发链条:

1. mutation 字段声明为**非空**,如 `updateItem(itemId: Int!): Item!`
2. 后端 resolver `throw` 异常 → GraphQL 返回 `{ data: null, errors: [{message}] }`(HTTP 仍是 200)
3. Relay 要把结果写进 store,发现「**声明非空的字段却拿到 null**」→ 契约被违反
4. Relay 抛出自己的错误,把两段拼在一起:
    
    ```
    No data returned for operation `XxxMutation`, got error(s): <后端真实消息>
    See the error `source` property for more information.
    ```
    

**真实消息被埋在 `error.source.errors[0].message` 里。**

## 三、关键区分:业务失败 ≠ GraphQL error

||后端做法|响应|是不是 GraphQL error|
|---|---|---|---|
|业务失败|`return { success: false, ... }`|`data` 完整,`errors` 空|❌ 不是,只是普通 data|
|抛异常|`throw SomeException`|`data` 变 null,`errors` 有值|✅ 是|

> `success: false` 只是写在 `data` 里的一个**值**,GraphQL 眼里这是一次**完全成功**的响应,Relay 不会报错。

这就是为什么 `createOrder` 的"前置条件未满足"分支不会出 "No data returned":它**不 throw,而是 `return { success:false, requiresSetup:true }`**。但 `createOrder` 的 throw 路径(没权限、状态不对)**照样会出**这个错误。

## 四、throw 之后,data 一定是 null 吗?—— 不一定,看 null 传播

resolver 一 throw,该字段先变 null,然后:

|抛错的字段|到根路径|data 结果|
|---|---|---|
|**可空**(`String` / `Item`)|—|data **不为 null**,只该字段 null,兄弟字段保留|
|**非空**,中间有可空祖先|冒泡到那个祖先|那个祖先变 null,data **不一定为 null**|
|**非空**,一路到根都非空(如根 mutation `Item!`)|顶到根|**整个 data 变 null**|

### 重要坑:null 传播会丢弃已算好的兄弟数据

```graphql
type Query { user: User }        # user 可空
type User { id: Int!  name: String! }   # name 非空
```

`id` 已成功算出 5,`name` throw → name 非空要冒泡 → 冒到可空的 `user` → `user: null` → **`id: 5` 也一起被丢弃**。

## 五、两个"中间 throw"别混淆

|"中间"|含义|结果|
|---|---|---|
|响应树里的中间字段|由该字段可不可空决定|可空→只它 null;非空→向上冒|
|一个 resolver 执行到一半 throw|字段是全有或全无|该字段直接算 null,没有"半份数据"|

对事务型 resolver(如 `updateItem` 在 `$transaction` 里跑一串):中途 throw →

- **数据库**:整个事务**回滚**,不留半成品(Prisma 保证)
- **响应**:函数无返回值 → 根级非空字段拿 null → `data: null` → 前端 "No data returned"

## 六、throw 与 return 互斥

同一次调用**要么 throw(啥都不返回)、要么 return(不抛异常)**,不可能既 throw 又 return。函数声明 `Item!` 的意思是:**当它正常返回时,保证是非空 Item**;一旦 throw,就走了另一条路,没有返回值。

## 七、怎么避免 / 处理 "No data returned"

两条路:

1. **后端改结构化返回(不 throw)**:`return { success:false, errorCode, message }`,字段可空。data 非空 → 无 Relay 错误 → 前端在 `onCompleted` 里读 `success` 判断。(= createOrder 模式,但要改 schema)
2. **前端解包**:保留 throw,从 `error.source.errors[0].message` 挖出真实消息单独 toast:
    
    ```ts
    onError(error) {
        const msg = (error as any)?.source?.errors?.[0]?.message;
        toast.error(msg || "操作失败");
    }
    ```
    

## 八、哪些代码会出现这个问题

**凡是「非空返回类型 + 会 throw 的 resolver」的 mutation/query,前端不特殊处理就会看到这句。** 例:同一个管理菜单里的 `deleteItem`、`cancelItem`、`createOrder`,只要走 throw 路径都会。


## Error返回值的具体返回位置

| 字段              | throw 后             | Relay 反应             | 前端在哪处理                      | 要额外做什么                     |
| --------------- | ------------------- | -------------------- | --------------------------- | -------------------------- |
| **非空 `Item!`** | 整个 data 变 null      | 抛 "No data returned" | `onError`                   | 从 `error.source` 挖消息       |
| **可空 `Item`**  | 只该字段 null,errors 有值 | 正常写入,不崩              | `onCompleted(data, errors)` | 判断 data 是否 null / 读 errors |
