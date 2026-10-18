---
title: "练习 1 - 自定义指令（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot 云智能体 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或 Copilot 需要了解背景信息，就应确保它能访问这些内容。[指令文件][instruction-files]可提供相关指导，让 Copilot 不仅理解要完成*什么*，还知道*如何*完成。

本练习将：

- 了解项目上下文、编码指南和文档规范如何通过存储库自定义指令及按路径限定的指令文件传递给 Copilot。
- 向 `.github/copilot-instructions.md` 添加新的存储库级规范。

> [!NOTE]
> 与 VS Code 和 CLI 学习路径不同，本练习不会使用更改前后的提示词进行对比。Copilot 云智能体会在 GitHub 议题上异步工作，因此难以实时并排展示影响。稍后的练习中审查云智能体生成的拉取请求时，会看到指令文件的影响。

## 指令文件

### 场景

Tailspin Toys 制定了一系列开发实践指南和要求：

- 数据层始终需要单元测试。
- UI 应采用深色模式并具有现代感。
- 代码中应使用 TSDoc 文档注释。
- 每个文件开头都应有说明文件用途的注释块。

通过指令文件，可以确保 Copilot 了解这些实践，并据此完成任务。

### 自定义指令

自定义指令可向 Copilot 提供上下文和偏好，帮助它理解代码风格和需求，从而生成更相关的建议和代码片段。你可以指定编码规范、库以及注释要求。指令既可以应用于整个存储库，也可以针对特定文件类型或任务。

指令文件有两种：

- `.github/copilot-instructions.md`：单个指令文件，会随存储库中的**每个**请求发送给 Copilot。它应包含项目级信息，例如技术栈、项目概述、最佳实践和适用于大多数聊天或 CLI 请求的全局指南。
- `.github/instructions/*.instructions.md`：可针对特定任务或文件类型提供指南，例如 TypeScript、Astro、UI 组件或单元测试。

> [!NOTE]
> 在 IDE 中，指令文件只用于 Copilot Chat 代码生成，不会用于代码补全或下一次编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时都会使用存储库级指令，以及带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot 还支持其他标准的指令文件，包括 AGENTS.md 和 CLAUDE.md。

### 管理指令文件的最佳实践

完整讨论指令文件超出本研讨会范围，但示例项目展示了一种可行做法：

- 在 `copilot-instructions.md` 中集中记录项目级指导，例如项目说明、结构和全局编码规范。
- 使用 `*.instructions.md` 文件为文件类型（如单元测试、Astro 组件和数据层）或特定任务提供说明。
- 使用自然语言，保持指南清晰，并提供代码示例。

创建指令文件没有唯一方法，使用 AI 也一样。通过实践，可以找到适合项目的做法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应有一套完善的指令文件。查看本项目文件时，可以看到针对 [UI 更新][ui-instructions]和 [Astro][astro-instructions]等任务的指令。
>
> Copilot 也可以帮助生成指令文件。不同界面的操作方式有所不同，例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`；相关课程会说明当前界面的具体方法。
>
> 想找模板或起点？可查看 [awesome-copilot][awesome-copilot]，其中收集了指令文件、自定义智能体和其他资源。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 探索项目中的自定义指令文件

花些时间查看存储库自带的指令文件：一个核心 `copilot-instructions.md` 文件，以及一组适用于不同任务的 `*.instructions.md` 文件。可在编辑器或 GitHub 网页界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 查看项目简介以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。注意 **Code standards** 下的 **GitHub Actions Workflows** 指导。这些内容适用于你与 Copilot 的所有交互。
3. 打开 `.github/instructions` 文件夹并查看其中内容。这里包含 Astro 文件、Drizzle 数据层、测试等指令。
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它设置相对于存储库根目录的 glob，决定哪些文件适用。例如，任何匹配 `**/*.test.ts` 的 TypeScript 测试文件都符合条件。
5. 查看项目中针对单元测试的具体指令。
6. 打开 `.github/instructions/drizzle.instructions.md` 并滚动到末尾。注意指向其他指令文件（如 `unit-tests.instructions.md`）及项目现有文件的链接。这样可以将大型指令集拆分成较小的可复用文件，并为 Copilot 提供生成代码时可参考的示例。（其中路径相对于指令文件，而不是存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目编码规范，但尚未要求在代码中添加文档。接下来会添加 TSDoc 文档注释和文件注释头的规则。

## 添加新的存储库规范

本练习中唯一需要手动编辑的内容，是添加项目级规则，要求在代码中使用 TSDoc 文档注释和文件级注释头。云智能体稍后处理分配给它的议题时会读取这些内容。

编辑前，先创建分支（云智能体会从议题目标分支读取指令，但仍应妥善提交自己的更改）：

1. 在 codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。现在记录存储库编码规范，改进代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分（通常在第 27 行附近）。查看其中的项目编码规范；它尚未包含代码文档要求，这就是生成的辅助函数没有文档注释的原因。
3. 在现有规范下方添加以下 Markdown，要求 Copilot 添加文件注释头和 TSDoc 文档注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 指令文件既可以放在存储库级 `.github/copilot-instructions.md` 中，为所有任务提供全局指导，也可以放在 `*.instructions.md` 文件中，针对特定语言、文件类型或任务提供指导。存储库级文件适合记录刚添加的文档注释等项目级规范。

## 提交、推送并合并指令更新

云智能体会从议题目标分支读取指令。下一练习将把议题分配给 Copilot，届时它会从 `main` 分支创建分支，因此必须先将指令更新合并到 `main`，云智能体才能读取。

1. 暂存并提交：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 到 `main` 的拉取请求并合并。下一练习中，云智能体处理议题时会读取这些指令。

> [!TIP]
> 如果希望在本研讨会中直接使用 `main`，可以跳过分支步骤，直接提交到 `main`。创建分支是为了模拟真实项目的处理方式。

## 总结与后续步骤

你已了解 Copilot 如何从项目指令文件中获取上下文，并向 `.github/copilot-instructions.md` 添加新的存储库级规范。之后会在云智能体生成的拉取请求中看到此规范的实际效果。

接下来，[将第一个议题分配给 Copilot 云智能体][next-lesson]。

## 资源

- [GitHub Copilot 指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更好 Copilot 自定义指令的 5 个建议][copilot-instructions-five-tips]
- [Awesome Copilot — 指令文件集合及其他资源][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
