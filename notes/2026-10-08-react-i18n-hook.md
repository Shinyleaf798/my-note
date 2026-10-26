---
title: "React Hook 的调用位置规则与可复用的翻译函数"
date: 2026-10-08
description: "useTranslations 只能在组件函数体或另一个 Hook 里调用;用自定义 Hook 在组件顶部换出一个普通函数,就能在回调里随便用。"
languages: ["TypeScript"]
frameworks: ["React", "next-intl"]
domains: ["前端"]
tags: ["概念"]
---

## 先搞清楚:什么是"组件函数"?

在 React 里,**一个返回画面(JSX)的函数,就是一个组件**。名字通常大写开头。

```tsx
// 这就是一个"组件函数"。MyButton 是函数名,return 里是要显示的画面
function MyButton() {
  return <button>点我</button>;
}
```

"**组件函数体**"= 就是这个函数 `{ }` 大括号里面的范围:

```tsx
function MyButton() {
  // 👇 这里面(从 { 到 return 之前)就叫"组件函数体"
  const x = 1;
  // 👆
  return <button>点我</button>;
}
```

## 什么是"另一个 Hook"?

Hook 就是**名字以 `use` 开头的函数**。React 自带一些(`useState`、`useEffect`、`useTranslations`),你也可以自己写一个 `use` 开头的,叫"**自定义 Hook**"。

```tsx
// 这就是一个"自定义 Hook",因为名字以 use 开头
function useTranslateLabel() {
  // 👈 这里面就是"另一个 Hook 里"
  const t = useTranslations();
  return (label) => t(label);
}
```

## 核心规矩:Hook 只能在这两个地方调用

`useTranslations()` 是 Hook。React 规定:**Hook 只能写在①组件函数体里,或者②另一个 Hook 里**。其它任何地方调用都会报错。

### ✅ 正确例子一:写在组件函数体里

```tsx
function MyButton() {
  const t = useTranslations();   // ✅ 在组件函数体顶部,合法
  return <button>{t("submit")}</button>;
}
```

### ✅ 正确例子二:写在另一个 Hook 里

```tsx
function useTranslateLabel() {     // 自己写的 Hook(use 开头)
  const t = useTranslations();     // ✅ 在另一个 Hook 里,合法
  return (label) => t(label);
}
```

### ❌ 错误例子:写在普通函数里

```tsx
// handleClick 是普通函数(不是组件、名字不是 use 开头)
function handleClick() {
  const t = useTranslations();   // ❌ 报错!不在那两个允许的地方
  toast.success(t("saved"));
}
```

### ❌ 错误例子:写在事件回调里

```tsx
function MyButton() {
  return (
    <button onClick={() => {
      const t = useTranslations();   // ❌ 报错!回调函数里不能调 Hook
      toast.success(t("saved"));
    }}>
      保存
    </button>
  );
}
```

## 这跟我们的需求有什么关系?

我们想在很多地方"翻译一段文字",包括 toast、点击回调里。但上面看到:**回调里、普通函数里不能直接调 `useTranslations()`**。

解决办法:**在组件函数体顶部先调一次(合法位置),拿到一个普通函数 `tl`,后面随便用**:

```tsx
function MyButton() {
  const tl = useTranslateLabel();   // ✅ 在组件函数体顶部调,合法。拿到普通函数 tl

  return (
    <button onClick={() => {
      toast.success(tl("saved"));   // ✅ tl 是普通函数,回调里随便用,不违规
    }}>
      {tl("save")}                  {/* ✅ 显示文字也能用 */}
    </button>
  );
}
```

关键点:`tl` 是**普通函数**(不是 Hook),所以哪里都能用;而它"翻译的能力"是在合法位置(组件顶部)通过 Hook 拿到的。

## 一句话总结

- **组件函数体** = 那个返回画面的函数,大括号里面、return 之前的区域。
- **另一个 Hook 里** = 在一个 `use` 开头的函数里面。
- Hook(`useTranslations`)只能在这两处调用;所以我们用一个 `use` 开头的 `useTranslateLabel` 在组件顶部调一次,换来一个能到处用的普通函数 `tl`。
