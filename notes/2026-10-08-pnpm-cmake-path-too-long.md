---
title: "React Native / Expo + pnpm + CMake 路径过深问题"
date: 2026-10-08
description: "pnpm 默认的非 hoisted 结构让 CMake 路径超长、Windows 上 Android build 失败,解法是 node-linker=hoisted 并清掉 native 缓存。"
frameworks: ["React Native", "Expo"]
tools: ["pnpm", "CMake", "Gradle"]
domains: ["移动端"]
tags: ["踩坑", "构建配置", "依赖管理"]
---

# React Native / Expo + pnpm + CMake 路径过深问题

## 问题现象

Windows 上跑 Android build 时出现类似错误：

```text
ninja: error: mkdir(...) No such file or directory
The object file directory has ... characters, maximum full path is 250
```

表面像是文件不存在，真正原因通常是路径太长。

---

## 根本原因

pnpm 默认 node_modules 结构很深：

```text
node_modules/.pnpm/<hash>/node_modules/package
```

CMake / Ninja build Android native module 时还会再叠加路径，最终超过 Windows / CMake 的路径限制。

所以这不是 C 盘或 G 盘的问题，而是路径长度问题。

---

## 解决方案

在项目根目录的 `.npmrc` 加：

```text
node-linker=hoisted
```

作用：把依赖从深层 `.pnpm/.../node_modules/...` 尽量提升成较短路径：

```text
node_modules/react-native-worklets
```

---

## 必做清理步骤

改成 hoisted 后要清旧依赖和 native 缓存：

```bash
rm -rf node_modules
pnpm install

rm -rf mobile/android
rm -rf mobile/.expo
cd mobile
npx expo prebuild --clean
npx expo run:android
```

如果只是普通 Gradle 缓存问题，可以先试：

```bash
cd mobile/android
./gradlew clean
cd ..
npx expo run:android
```

---

## 记忆点

- pnpm 默认结构深，React Native native build 容易撞路径限制。
- `node-linker=hoisted` 用来缩短 node_modules 路径。
- 改 node_modules 结构后，一定要清 Expo / Gradle 旧缓存。
- native module 项目尽量放短路径，例如 `G:\app`、`D:\rn`。
