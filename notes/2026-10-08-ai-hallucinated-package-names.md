---
title: "AI 生成的包名可能根本不存在——装之前先查 npm registry"
date: 2026-10-08
description: "AI 会幻觉出不存在的包名,这类名字也可能被他人抢注并塞入恶意代码;安装前用 npm registry 核对。"
tools: ["npm"]
domains: ["AI辅助开发"]
tags: ["踩坑", "供应链安全", "依赖管理"]
---

AI幻觉错误的包比如npm view react-codemorph这种更本完全没有的包或者是别人先注册这个包接着再加入恶意代码导致项目被污染。

可以使用npm registry来查看包是否是正常的。
