---
title: "跨仓库触发 GitHub Actions:token 权限给谁、secret 存哪"
date: 2026-10-18
description: "A 仓库 push 后通知 B 仓库重新构建。PAT 的权限范围要指向 B,但 secret 必须存在 A —— 存错地方 GitHub 不报错,只给你一个空字符串。"
tools: ["GitHub Actions", "CI/CD"]
domains: ["DevOps"]
tags: ["踩坑"]
---

# 跨仓库触发 GitHub Actions:token 权限给谁、secret 存哪

## 场景

两个仓库:`notes-repo`(内容) 和 `site-repo`(站点)。想要 push 笔记之后,站点自动重新构建。

做法是在 `notes-repo` 里跑一个 workflow,调 GitHub API 向 `site-repo` 发一个
`repository_dispatch` 事件,`site-repo` 那边监听这个事件触发构建。

## 容易搞混的地方

配置分两步,而且**指向两个不同的仓库**:

| | 问的是什么 | 填哪个仓库 |
| --- | --- | --- |
| PAT 的 Repository access | 这把钥匙能开**哪扇门** | `site-repo` |
| Secret 存在哪个仓库 | 这把钥匙放在**谁口袋里** | `notes-repo` |

钥匙开的是 `site-repo` 的门,但拿钥匙的人是 `notes-repo`。

### 为什么 secret 要存在发起方

**Secret 是仓库级的私有存储,只有该仓库自己的 workflow 读得到。** 跑这个 workflow 的是
`notes-repo` 的 runner,它只能看见 `notes-repo` 的 secrets。存到 `site-repo` 去,
对 `notes-repo` 来说等于不存在。

### 为什么权限要给目标方

Token 的权限范围决定它**能操作谁**。发起方这边不需要任何额外权限 —— workflow 跑在自己
仓库里,用的是自带的 `GITHUB_TOKEN`。需要授权的只有"去敲目标仓库的门"这一下。

> 一句话:**权限跟着目标走,存放跟着发起方走。**

## 失败时的样子

Secret 存错仓库,`${{ secrets.XXX }}` 会被**静默替换成空字符串**。GitHub 不会报
"secret 不存在",也不会有任何警告。

所以 workflow 里要自己检查:

```yaml
    env:
      TOKEN: ${{ secrets.PORTFOLIO_DISPATCH_TOKEN }}
    run: |
      if [ -z "$TOKEN" ]; then
        echo "::error::TOKEN 没有设置"
        exit 1
      fi
```

secret 读不到时的常见原因,按可能性排:

1. 建在了**目标仓库**而不是发起方仓库
2. 建在了 **Variables** 分页而不是 Secrets 分页(那要用 `${{ vars.X }}` 读)
3. 建成了 **Environment secret**,但 job 没声明 `environment:`
4. 这次 run **早于** secret 创建 —— secret 是运行时读的,重跑一次即可

## 另一半:curl 的退出码不代表请求成功

`curl -sS -X POST ...` 对 4xx/5xx **也返回退出码 0**。所以上面那个空 token 如果没被
拦住,会发出 `Authorization: Bearer `,GitHub 返回 401,而 **job 仍然是绿的**。

结果是最坏的一种失败:看板全绿,站点再也不更新,而且没有任何信号。

显式检查状态码:

```bash
status=$(curl -sS -o response.txt -w '%{http_code}' -X POST \
  -H "Authorization: Bearer $TOKEN" \
  https://api.github.com/repos/<owner>/<target-repo>/dispatches \
  -d '{"event_type":"content-updated"}')

if [ "$status" != "204" ]; then
  echo "::error::触发失败 (HTTP $status)"
  cat response.txt
  exit 1
fi
```

`repository_dispatch` 成功时返回 **204 No Content**,响应体是空的 —— 不要去判断响应内容。

## 权限到底要勾哪个

两个端点要的权限不一样,别弄反:

| 端点 | 事件 | fine-grained token 需要 |
| --- | --- | --- |
| `POST /repos/{owner}/{repo}/dispatches` | `repository_dispatch` | Contents: write |
| `POST /repos/{owner}/{repo}/actions/workflows/{id}/dispatches` | `workflow_dispatch` | Actions: write |

不确定的时候不要猜:**跑一次看状态码**。403 的响应体会直接写明缺哪个权限。

## 记忆点

- 权限跟着目标走,存放跟着发起方走
- 读不到的 secret 是空字符串,不是报错 —— 自己加非空检查
- `curl` 退出码 0 不代表 HTTP 成功 —— 自己判断状态码
- 这两条是同一类坑:**失败被当成成功,而且看起来一切正常**
