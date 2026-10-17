---
title: "React Developer 面试复习笔记"
date: 2026-10-14
description: "从技术栈、Git 协作、CI/CD、测试到项目叙述与 AI 辅助开发的面试复习整理,重点是理解每个技术解决什么问题,而不是背题。"
languages: ["TypeScript", "JavaScript"]
frameworks: ["React", "Next.js", "React Native"]
tools: ["Git", "GitHub Actions", "CI/CD"]
domains: ["前端", "AI辅助开发"]
tags: ["面试", "概念", "测试", "Git协作", "除错方法"]
---

# React Developer 面试复习笔记

这份笔记用于理解前端开发中的核心概念，而不是单纯背诵面试题。重点是知道每个技术是什么、解决什么问题，以及它们如何组合成完整的开发流程。

---

# 1. React 与前端技术栈

## 1.1 什么是技术栈（Tech Stack）？

技术栈是一个项目使用的一组技术、工具和框架。一个 React 项目通常不只包含 React，还可能包含语言、构建工具、UI 组件库、路由、测试工具和部署平台。

可以把技术栈理解为：**开发一个应用时，负责不同工作的工具组合。**

## 1.2 React 及相关技术的分类

| 分类 | 常见技术 | 作用 |
|---|---|---|
| UI 库 | React | 使用组件构建用户界面，管理 UI 状态与渲染 |
| 编程语言 | JavaScript、TypeScript | 编写应用逻辑；TypeScript 在 JavaScript 基础上增加静态类型检查 |
| 语法扩展 | JSX、TSX | 在 JavaScript 或 TypeScript 中编写类似 HTML 的 UI 描述 |
| Web 框架 | Next.js、Remix | 在 React 之上提供路由、服务端渲染等应用框架能力 |
| 构建工具 | Vite、Webpack | 提供开发服务器，并处理、打包应用代码 |
| UI 组件库 | Radix UI、MUI、Ant Design | 提供可复用的交互组件或完整 UI 组件 |
| 样式方案 | CSS、CSS Modules、Tailwind CSS | 控制页面布局、外观与响应式样式 |
| 路由 | React Router、Next.js Router | 根据 URL 显示不同页面或视图 |
| 状态管理 | useState、useReducer、Context、Redux、Zustand | 管理组件或跨组件共享的数据状态 |
| 测试工具 | Vitest、Jest、React Testing Library、Playwright | 检查函数、组件或完整用户流程是否正常 |
| 移动应用 | React Native、Expo | 使用 React 开发移动应用；Expo 提供 React Native 的开发与构建工具 |
| 版本控制 | Git、GitHub | 追踪代码变化、协作开发和托管代码 |

## 1.3 React、Next.js、React Native 有什么区别？

- **React**：用于构建 UI 的核心库。
- **Next.js**：基于 React 的 Web 框架，提供路由、服务端渲染和其他应用功能。
- **React Native**：使用 React 的组件与开发模型构建原生移动应用，渲染目标不是普通网页 DOM。
- **Expo**：围绕 React Native 提供开发、测试、构建和发布工具。
- **Vite**：开发服务器与构建工具，不是 React 的替代品，也不是与 Next.js 完全相同类别的工具。

## 1.4 React 的核心概念

### Components（组件）

把 UI 拆分成可复用、可维护的小部分。例如 Button、Modal、TicketCard。

### Props

组件从外部接收的数据。Props 通常由父组件传入，组件不应直接修改传入的 props。

### State

组件需要记住、并可能随用户交互或应用行为变化的数据。例如弹窗是否打开、输入框内容或当前选中的选项。

### Hooks

让函数组件使用 React 功能的函数。常见例子：

- `useState`：管理组件状态。
- `useEffect`：让组件与外部系统同步，例如订阅或事件监听；需要正确处理依赖和清理。
- `useContext`：读取 Context 中共享的数据。
- `useRef`：保存不会因每次渲染而重置的引用值，也可引用 DOM 元素。

### Re-render（重新渲染）

当组件的 state、props 或相关上下文发生变化时，React 可能重新执行组件函数并更新 UI。重新渲染不等于整个页面都被重新加载。

### Controlled Component（受控组件）

表单值由 React state 控制。适合需要实时验证、条件显示或统一管理输入数据的场景。

### Reusable Components（可复用组件）

将重复的 UI 或行为抽象成组件，减少重复代码并保持界面一致。但不应该为了复用而过度抽象。

## 1.5 如何描述自己的 React 技术栈？

介绍技术栈时，可以按这个顺序说明：

1. **应用类型**：React Web，还是 React Native / Expo。
2. **语言**：JavaScript 还是 TypeScript。
3. **框架与构建工具**：Next.js、Vite 或其他工具。
4. **UI 与样式**：Radix UI、其他组件库、CSS 或 Tailwind CSS。
5. **路由与状态管理**：项目实际使用什么。
6. **测试与部署**：是否配置自动化测试和部署。

不要把自己没使用过的工具说成有实战经验。React 版本可从项目的 `package.json` 或包管理器命令确认。

---

# 2. Git 与团队协作：如何完成开发工作

## 2.1 什么是 Git？

Git 是分布式版本控制系统，用于记录代码变化、查看历史、创建分支、合并代码，以及在需要时恢复之前的状态。

**GitHub** 是托管 Git 仓库的协作平台，提供 Pull Request、Code Review、Issue 和自动化工作流等功能。Git 和 GitHub 不是同一个东西。

## 2.2 Git 的核心概念

| 概念 | 解释 |
|---|---|
| Repository（仓库） | 项目文件及其版本历史 |
| Working Directory（工作区） | 当前正在编辑的文件 |
| Staging Area（暂存区） | 下一次 commit 准备包含的修改 |
| Commit | 对一组修改建立一个版本记录 |
| Branch（分支） | 独立推进一条开发历史的指针 |
| Remote（远程仓库） | 托管在远端的仓库，例如 GitHub 上的仓库 |
| Push | 将本地提交发送到远程仓库 |
| Pull | 获取远程变化并整合到当前分支，通常结合 fetch 与 merge 或配置的 rebase |
| Fetch | 获取远程提交与引用信息，但不直接整合到当前分支 |
| Pull Request（PR） | 提交代码变更供团队审查、讨论与合并 |
| Merge | 将不同分支的变更整合在一起 |
| Rebase | 将提交重新应用到另一个基点上，可能改写提交历史 |
| Merge Conflict | Git 无法自动判断如何整合某些冲突修改的情况 |
| Stash | 暂时保存尚未提交的修改，方便切换任务或分支 |
| Revert | 通过一个新 commit 反转已有 commit 的更改 |
| Reset | 移动当前分支的指针，并根据选项调整暂存区或工作区 |

## 2.3 常见团队开发流程

一个常见的 Feature Branch 工作流程如下：

1. 从最新的主分支创建功能分支。
2. 在功能分支上编写和修改代码。
3. 在本地运行相关检查或测试。
4. 使用 `git add` 将需要提交的修改放入暂存区。
5. 使用 `git commit` 保存一组逻辑清晰的修改。
6. 使用 `git push` 推送分支到远程仓库。
7. 创建 Pull Request，说明改动内容和目的。
8. 团队成员进行 Code Review，并根据反馈修改。
9. 检查自动化测试或 CI 结果。
10. 通过审查和检查后，将分支合并到目标分支。

实际团队可能使用不同的分支策略、审批规则和发布流程。

## 2.4 常见命令与用途

```bash
# 查看当前分支与工作区状态
git status

# 创建并切换到新分支
git switch -c feature/my-feature

# 将指定文件放入暂存区
git add path/to/file

# 创建 commit
git commit -m "Add ticket validation"

# 推送当前分支，并设置上游分支
git push -u origin feature/my-feature

# 获取远程更新，但不自动合并
git fetch origin

# 暂存尚未提交的修改
git stash

# 恢复 stash 中的修改并保留该 stash 记录
git stash apply

# 创建一个新 commit 来撤销指定 commit 的更改
git revert <commit-hash>
```

### `reset`、`revert` 和 `stash` 的区别

- `git reset --soft HEAD~1`：撤销最近一次 commit，修改保留在暂存区。
- `git reset HEAD~1`：默认等同于 `--mixed`，撤销 commit 并将修改保留在工作区、取消暂存。
- `git reset --hard HEAD~1`：会丢弃相关的未保留修改，使用前必须确认，避免丢失工作。
- `git revert <commit>`：创建新 commit 来反转指定提交，通常更适合已经共享或 push 的历史。
- `git stash`：暂存未提交的工作，不是撤销已经创建的 commit。

## 2.5 什么是 Code Review？

Code Review 是由其他开发者检查代码变更的过程，通常会关注：

- 功能是否正确。
- 是否处理边界情况。
- 代码是否易读、易维护。
- 是否有安全或性能问题。
- 是否符合项目现有架构与规范。
- 是否有适当的测试。

Code Review 不只是找错，也能帮助团队共享知识并保持代码质量。

## 2.6 遇到 Merge Conflict 怎么办？

1. 查看 Git 标记出的冲突文件。
2. 阅读冲突双方的修改，理解各自目的。
3. 手动整理成正确的最终内容，移除冲突标记。
4. 运行测试或检查，确认合并后的逻辑正确。
5. 使用 `git add` 标记冲突已解决。
6. 根据当前操作继续完成 merge 或 rebase。

不要只为了让 Git 命令成功就随意删除一方的修改。

---

# 3. CI/CD：代码如何自动检查、构建与部署

## 3.1 什么是 CI/CD？

CI/CD 是软件开发中的自动化实践，帮助团队更频繁、更可靠地整合、验证和发布代码。

- **CI — Continuous Integration（持续集成）**：开发者经常将代码变更整合到共享分支，并通过自动化检查尽早发现问题。
- **Continuous Delivery（持续交付）**：自动构建、测试并准备好可发布的版本，生产发布通常仍可能需要人工批准。
- **Continuous Deployment（持续部署）**：通过所有规定检查的变更可以自动部署到生产环境，无需每次人工批准。

在不同团队中，“CD”可能泛指持续交付或持续部署，所以面试时可以明确自己说的是哪一种。

## 3.2 一个典型的 CI/CD Pipeline

```text
Developer changes code
        |
        v
Commit and push / Open Pull Request
        |
        v
CI workflow is triggered
        |
        v
Install dependencies
        |
        v
Lint / Type checks / Automated tests
        |
        v
Build application
        |
        v
Checks pass?
   /          \
 No            Yes
 |              |
Review logs     v
Fix and retry  Prepare release / Deploy
                    |
                    v
              Verify deployment
```

这只是一个常见示例。实际项目不一定包含所有步骤，也可能把 CI 和部署拆成不同 workflow。

## 3.3 Pipeline 中的主要步骤

| 步骤 | 作用 |
|---|---|
| Trigger（触发条件） | 例如 push、Pull Request、定时任务或手动触发 |
| Install dependencies | 安装项目依赖 |
| Lint | 检查代码风格及部分常见错误 |
| Type check | 检查 TypeScript 类型问题 |
| Automated tests | 自动执行测试 |
| Build | 生成可交付的构建产物 |
| Artifact | 构建产生、可保存或传递给后续步骤的文件 |
| Deploy | 发布到测试、预发布或生产环境 |
| Verification / Monitoring | 验证部署结果，并观察应用是否正常 |

Lint、类型检查、测试和 Build 是不同的检查。Build 成功不代表所有功能都正确；没有自动化测试的 pipeline 也不一定能发现逻辑错误。

## 3.4 常见 CI/CD 工具

| 工具或平台 | 常见用途 |
|---|---|
| GitHub Actions | 在 GitHub 仓库中配置自动化 workflow |
| GitLab CI/CD | 在 GitLab 中配置 pipeline |
| Jenkins | 可高度定制的自动化服务器 |
| CircleCI | 执行持续集成与交付任务 |
| Vercel | 常用于 Web 应用的构建与部署，并支持与 Git 仓库集成 |
| Netlify | 常用于 Web 网站的构建与部署 |

这些工具的职责可能重叠，但并不完全相同。应根据项目实际配置描述自己使用的工具。

## 3.5 GitHub Actions 的基本概念

- **Workflow**：定义触发条件和自动化流程的配置。
- **Job**：workflow 中的一组任务，通常运行在一个 runner 上。
- **Step**：job 内的单个操作，例如安装依赖或运行测试。
- **Runner**：实际执行 job 的环境。
- **Secret**：存储 Token、部署凭证等敏感值，避免直接写进代码仓库。

一个 workflow 可以在 Pull Request 时执行检查，也可以在合并到指定分支后执行部署。具体行为取决于配置。

## 3.6 Pipeline 失败时如何处理？

1. 找到失败的 workflow、job 和 step。
2. 查看日志，判断失败原因。
3. 确认是代码错误、测试失败、依赖问题、环境配置还是权限问题。
4. 修复问题并在适当情况下先本地验证。
5. 推送修复或重新运行 workflow。
6. 检查所有必要步骤通过后，再继续部署。

如果部署由这些检查结果控制，检查失败的版本不应进入被保护的发布流程。

## 3.7 安全与发布注意事项

- 不要把 API Key、密码或部署凭证直接写进仓库。
- 使用平台的 Secrets 或安全的凭证管理机制。
- 对生产部署使用适当权限、审批与分支保护。
- 尽量让构建和测试可重复。
- 需要时准备回滚方案，并确认部署后的应用是否正常。

## 3.8 如何诚实描述自己的经验？

- 如果实际配置并维护过 pipeline，可以说明工具、触发条件、检查步骤和部署目标。
- 如果项目自动部署，但你没有配置过，可以说自己使用过该流程，并解释实际观察到的步骤。
- 如果只使用 Git push、手动测试和手动部署，应说明自己理解 CI/CD 原理，但尚未独立配置完整 pipeline。

**Git push 本身不等于 CI/CD。** 只有项目配置了相应自动化流程，才会在触发后自动执行检查、构建或部署。

---

# 4. 测试与开发习惯：如何保证代码质量

## 4.1 什么是软件测试？

软件测试是通过检查代码或应用行为，确认它是否符合预期，并尽早发现错误。

测试不只是“点开页面看看”。它可以在不同层次进行，从单个函数到完整用户流程。

## 4.2 测试类型

| 类型 | 测试目标 | 例子 |
|---|---|---|
| Unit Test（单元测试） | 单独验证一个小单位，例如函数 | 验证价格计算函数 |
| Component Test（组件测试） | 验证组件的渲染与交互 | 点击按钮后弹窗是否打开 |
| Integration Test（集成测试） | 验证多个模块一起工作 | 表单、验证逻辑和 API 层是否正确协作 |
| End-to-End Test（端到端测试） | 从用户视角验证完整流程 | 用户登录后创建并提交表单 |
| Manual Testing（手动测试） | 人工操作应用并检查结果 | 在浏览器中验证不同屏幕尺寸和错误提示 |
| Regression Testing（回归测试） | 确认修改没有破坏原有功能 | 修复表单后，再测试已有的提交流程 |

不同团队对组件测试与集成测试的边界定义可能略有差异，重点是理解它们验证的范围。

## 4.3 常见测试工具

- **Vitest / Jest**：运行 JavaScript 或 TypeScript 测试。
- **React Testing Library**：从用户交互和可观察行为的角度测试 React 组件。
- **Playwright**：在浏览器中执行端到端测试。
- **Browser DevTools**：检查 Console、Network、DOM、请求和运行时错误。

使用哪些工具取决于项目，并非每个项目都会使用全部工具。

## 4.4 什么是 Lint、Type Check 和 Build？

| 检查 | 主要用途 | 不能保证什么 |
|---|---|---|
| Lint | 检查代码规范和部分潜在错误 | 不能证明功能一定正确 |
| Type Check | 检查类型是否符合声明 | 不能发现所有运行时问题 |
| Build | 检查应用能否完成构建并生成产物 | 构建成功不代表所有用户流程都正常 |
| Automated Tests | 检查已覆盖的行为是否符合预期 | 不能保证未覆盖的行为没有问题 |

这些检查可以组合进 CI pipeline。

## 4.5 如何系统地 Debug？

一个可重复使用的 Debug 流程：

1. **Reproduce**：稳定复现问题，记录输入和操作步骤。
2. **Observe**：查看错误消息、Console、Network 和相关日志。
3. **Isolate**：缩小范围，确认问题来自哪个组件、函数、请求或状态。
4. **Hypothesize**：提出可能原因。
5. **Verify**：通过最小实验或调试工具验证原因，而不是猜测。
6. **Fix**：实施范围尽量清晰的修复。
7. **Test**：验证修复后的行为，并检查相关功能是否受到影响。
8. **Prevent recurrence**：必要时增加测试、日志或更清晰的类型约束。

## 4.6 Code Review 与可维护性

写出能运行的代码只是目标之一；代码也应该方便其他开发者理解和修改。

良好的开发习惯包括：

- 使用清楚、一致的命名。
- 将组件职责保持在合理范围内。
- 避免不必要的重复代码，但不要过度抽象。
- 处理加载、成功、空数据和错误状态。
- 关注表单验证与边界条件。
- 让修改范围清晰、便于 review。
- 对重要逻辑编写适当测试。
- 遵循项目既有的代码风格与架构。

## 4.7 常见的 React 质量检查场景

以表单为例，开发时可以检查：

- 必填字段为空时，是否显示正确提示？
- 输入不合法时，是否阻止提交？
- 请求失败时，用户能否看到错误信息？
- 提交期间是否防止重复操作？
- 成功提交后，页面状态是否正确更新？
- 修改组件后，原有功能是否仍然正常？

这些检查既可以手动进行，也可以将稳定、重要的行为写成自动化测试。

---

# 5. 项目经验、沟通与 AI 辅助开发

这一部分关注的是：你如何完成任务、解释自己的决策，并确认代码质量，而不只是知道技术定义。

## 5.1 如何介绍一个项目？

可以按照以下顺序介绍：

1. **Project overview**：项目解决什么问题、面向什么用户。
2. **Tech stack**：使用哪些语言、框架、库和工具。
3. **My responsibilities**：自己实际负责哪些功能。
4. **Technical challenge**：遇到什么具体问题。
5. **Solution**：如何分析并实施解决方案。
6. **Verification**：如何确认结果正确。
7. **Outcome / Learning**：结果如何，以及学到了什么。

最好选择一个真实、具体的案例，能够解释自己的代码改动和思考过程。

## 5.2 如何解释一次技术问题的解决过程？

使用这个结构：

- **Situation**：当时发生了什么？
- **Problem**：具体问题是什么？
- **Investigation**：如何找到原因？
- **Action**：采取了什么修改？
- **Verification**：如何验证？
- **Result**：最终结果是什么？

不要只说“我问了 AI，然后修好了”。应说明自己如何判断建议是否正确，以及最终如何验证。

## 5.3 AI 辅助开发

AI 工具可以帮助开发者：

- 理解不熟悉的代码。
- 生成实现方案或修改草稿。
- 解释错误信息。
- 提出测试场景。
- 比较不同实现方式。
- 编写文档或整理代码。

但 AI 的输出可能不正确，也可能不了解项目中的隐含约束。开发者仍需负责检查和验证。

一个较稳妥的流程是：

1. 明确需求与验收条件。
2. 阅读相关组件、类型、调用关系和项目约定。
3. 让 AI 提出方案或修改建议。
4. Review 具体差异（diff），检查范围是否合理。
5. 理解关键逻辑与潜在副作用。
6. 运行 Lint、Type Check、测试或 Build。
7. 手动验证用户实际会使用的流程。
8. 确认完成后再提交代码。

## 5.4 如何说明自己使用 AI，但仍然理解代码？

可以解释自己的工作方式：

- AI 用来辅助理解、探索方案和提高效率。
- 自己负责确认需求、审查修改和判断设计是否合适。
- 对关键逻辑会追踪调用关系，并确认 Props、State、类型与副作用。
- 通过测试和实际操作验证结果。
- 不理解的代码不会因为能运行就直接接受。

应根据真实工作方式描述经验，不要声称做过自己没有做过的代码审查、测试或架构设计。

---

# 6. 面试前的自我检查

## React 与技术栈
- [ ] 能解释 React、TypeScript、Next.js、React Native 的区别。
- [ ] 能解释 Props、State、Hooks 和组件重新渲染。
- [ ] 能从项目配置中确认实际使用的版本和工具。
- [ ] 能说明 UI 组件库与 React 本身的区别。

## Git 与协作
- [ ] 理解工作区、暂存区、commit、branch 和 remote。
- [ ] 能说明日常的分支开发与 Pull Request 流程。
- [ ] 理解 merge、rebase、reset、revert 和 stash 的用途。
- [ ] 知道如何处理 merge conflict。

## CI/CD
- [ ] 能区分 CI、Continuous Delivery 和 Continuous Deployment。
- [ ] 能描述一个典型 pipeline 的步骤。
- [ ] 理解 Workflow、Job、Step 和 Runner。
- [ ] 知道如何调查 pipeline 失败。
- [ ] 能准确说明自己项目的实际自动化程度。

## 测试与开发习惯
- [ ] 能区分单元、组件、集成和端到端测试。
- [ ] 理解 Lint、Type Check、Build 和 Tests 的区别。
- [ ] 能描述系统化 Debug 流程。
- [ ] 知道如何验证修改并避免回归问题。

## 项目经验与沟通
- [ ] 能介绍一个真实项目及自己的职责。
- [ ] 能解释一个具体的技术问题和解决过程。
- [ ] 能说明如何 Review 与验证 AI 生成的代码。
- [ ] 能诚实区分实际经验、理论知识和仍在学习的内容。

---

# 总结

这五个主题分别回答不同的问题：

1. **React 与技术栈**：应用由哪些技术组成，每项技术负责什么。
2. **Git 与团队协作**：代码如何被追踪、共享、审查和合并。
3. **CI/CD**：代码如何通过自动化检查、构建并交付或部署。
4. **测试与开发习惯**：如何发现错误、验证修改并维护代码质量。
5. **项目经验与 AI 辅助开发**：如何完成实际任务、解释决策并对交付结果负责。

面试准备的目标不是记住所有工具名称，而是能够解释每项技术解决什么问题、它与其他部分如何协作，以及自己在真实项目中做过什么。
