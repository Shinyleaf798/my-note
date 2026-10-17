---
title: "Stripe Webhook 本地监听配置"
date: 2026-10-08
description: "stripe listen 要同时给 --api-key 和 --forward-to,否则监听的是错的账号、或事件只停在 terminal 进不到本地 server。"
tools: ["Stripe CLI"]
domains: ["后端"]
tags: ["踩坑", "webhook", "本地开发"]
---

# Stripe Webhook 本地监听配置

## 推荐命令

```bash
stripe listen --api-key <STRIPE_SECRET_KEY> --forward-to localhost:4000/api/stripe/webhook
```

> 不要把真实 Stripe secret key 写进笔记或提交到仓库。

---

## 为什么要加 `--api-key`

Stripe CLI 默认可能连接你自己的 Stripe 账号，但项目付款走的是项目 `.env` 里的 Stripe 账号。

如果账号不一致，本地会监听不到项目付款事件。

---

## 为什么要加 `--forward-to`

生产环境 webhook 会发到公开 URL。本地开发没有公开 URL，所以需要 Stripe CLI 转发到本地 server：

```text
Stripe -> Stripe CLI -> localhost:4000/api/stripe/webhook
```

没有 `--forward-to`，事件只会显示在 terminal，server 收不到。

---

## 常见错误

| 情况 | 结果 |
|---|---|
| 用错 Stripe 账号 | 项目付款事件收不到 |
| 没有 `--forward-to` | terminal 看得到事件，但 server 收不到 |
| 两个都缺 | 付款完成后，本地业务逻辑不会触发 |

---

## 关于 `stripe login --project-name`

可以用 profile 登录：

```bash
stripe login --project-name <project> --api-key <STRIPE_SECRET_KEY>
stripe listen --project-name <project> --forward-to localhost:4000/api/stripe/webhook
```

但如果每次启动 Stripe 都产生不同 webhook signing secret，还是直接用 `--api-key` 方式更明确。
