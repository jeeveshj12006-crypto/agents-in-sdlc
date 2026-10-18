---
title: "练习 2 - 使用 Copilot 智能体模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是最简单的应用更新，通常也需要修改多个文件并执行相应操作，例如运行测试。开发人员往往需要找到所有相关文件、进行更改、运行测试、调试、找出遗漏的文件，再次修改……工作还会不断延伸。

这正是 Copilot 智能体模式的用武之地。

Copilot 智能体模式可在 IDE 中自主工作。它会先探索现有项目结构，执行必要更新，运行测试等任务，并自动修复发现的问题。现在了解如何通过智能体模式为网站添加新功能。

> [!NOTE]
> 尽管名称相似，智能体模式和云智能体提供的是两种不同体验。智能体模式在 IDE 中执行任务，便于快速反馈和交互。云智能体则像团队中的同事一样异步工作，通过议题和拉取请求与你协作。

本练习将了解：

- Copilot 智能体模式如何探索项目、找出相关文件并协调修改。
- GitHub Copilot 智能体模式如何跨 UI 和数据层实现功能。
- 合并代码前如何审查 Copilot 智能体模式生成的更改和测试。

## 场景

随着游戏列表不断扩大，你希望用户可以按类别和发行商筛选。上一练习已添加发行商辅助函数；现在使用 Copilot 智能体模式完成剩余的数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

更改代码之前，先了解 Tailspin Toys 网站当前的功能。

该网站是以开发者为主题的桌游众筹平台，用户可以查看游戏列表及其详情。它是一个 Astro 应用，在构建时将页面渲染为静态 HTML。页面通过 Drizzle ORM 在 frontmatter 中直接查询本地 SQLite 数据库，不存在单独的后端 API 或客户端 UI 框架。可复用的数据访问辅助函数位于 `src/lib/`，交互功能通过使用标准 DOM API 的小型作用域 Astro `<script>` 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单个 Astro 开发服务器。按照以下步骤在 GitHub Codespace 中运行：

1. 返回 codespace。上一练习结束后，当前应在 `main` 分支。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux），在 Codespace 中打开新终端。
3. 创建并切换到筛选功能分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器准备就绪后，会显示包含 URL 的横幅，类似如下：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果弹出对话框询问是否打开 `http://localhost:4321` 浏览器窗口，请选择 **x** 关闭。

5. 在终端中按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后选择 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 codespace 中，选择终端里的 localhost URL 会自动跳转到 `https://<your-codespace-name>-4321.app.github.dev/`。这是通往 codespace 的私有隧道，现在网站服务器已在其中运行。

### 浏览网站

网站启动后，可以探索其功能：

- **主页**：显示桌游列表及标题、图像和描述。
- **游戏详情页**：选择游戏后会打开详情页，其中包含标题、描述、发行商和类别等信息。

## 使用 Copilot 探索待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始干净的新对话。模式和模型选择会因练习而异，相关步骤会注明。

网站初始实现已可运行，但还可以增加新功能。先查看项目待办事项。通过模板设置项目时，系统已自动创建 GitHub 议题；让 GitHub Copilot 列出这些议题。

1. 在 Chat 视图的智能体下拉菜单中选择 **Agent**。**Agent** 可自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中智能体选择器的截图。](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本研讨会作者并未偏好某个模型。编写本研讨会时使用的是 Claude Sonnet 4.5，因此文档采用此模型，以期让建议相对一致、带来良好体验。不过，LLM 具有概率性，建议可能与本研讨会描述不同，这是正常且可预期的。

> [!NOTE]
> 由于 LLM 具有概率性，Copilot 可能会使用不同的 MCP 命令，但仍应能够完成任务。

3. 向 Copilot 发送以下提示词，询问存储库中的待办议题：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue**，运行列出所有议题的命令。
5. 审查生成的议题列表。

请注意，Copilot 还根据对用户的价值为议题排定了优先级。

## 审查指令文件

让智能体生成代码之前，先查看用于为 Copilot 提供上下文的指令文件。接下来会使用 [UI 指令文件](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)，了解如何为网站添加功能。

1. 在 codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 查看添加功能的总体指南，包括：
   - 架构概述。
   - 组件设计、可测试性和无障碍原则。
   - 指向不同文件类型具体指令的链接，包括 Astro、Tailwind CSS 和 Drizzle。

> [!TIP]
> 指令文件可以引用其他指令文件和项目文件。路径相对于指令文件所在位置。这样既可复用内容、拆分复杂指令，也能提供示例和模板。

## 实现筛选功能

完成筛选至少需要对应用进行三项独立更新：

- 添加或完善数据层中的筛选逻辑 (`src/lib/`)。
- 添加或更新筛选行为测试。
- 更新游戏列表页，添加筛选 UI。

此外，在合并之前必须运行并通过测试。Copilot 智能体模式可以完成这些任务。

1. 继续当前 Copilot 对话，或选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions** 和 **ui** 指令文件。

   ![选择 UI 指令文件的示例截图](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉菜单仍选中 **Agent**。
4. 确认模型仍为 **Claude Sonnet 4.5**。
5. 使用以下提示词，让 Copilot 根据待办议题实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 如何探索项目、查找功能相关文件，包括数据层辅助函数、UI 和测试。然后它会修改文件并运行测试。

   ![Copilot 探索项目文件的截图](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会执行多项操作，例如探索项目、修改文件和运行测试。根据任务复杂度和代码库规模，这可能需要几分钟。过程中，代码编辑器可能会出现 **Keep** 和 **Undo** 按钮。Copilot 完成后，所有更改都会显示相应的 **Keep** 或 **Undo** 按钮，因此无需在工作进行时选择。

7. Copilot 提示时，选择 **Continue** 运行测试。

   ![Copilot Chat 面板中要求确认运行测试的对话框截图](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能出现暂停，甚至有测试失败。这没有关系。Copilot 会在代码生成和测试之间反复迭代，直到任务完成且未发现错误。

   ![Copilot 智能体模式完整 Chat 会话的截图](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码，查找潜在问题。

> [!CAUTION]
> 请务必审查 Copilot 或其他 AI 工具生成的代码。

10. 返回正在运行的网站，在浏览器中体验新功能。
11. 确认功能正常并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结与后续步骤

恭喜！本练习中，你使用 GitHub Copilot 智能体模式为 Tailspin Toys 网站添加了新功能。你了解了：

- GitHub Copilot 智能体模式如何跨 UI 和数据层实现功能。
- Copilot 智能体模式如何探索项目、找出相关文件并协调修改。
- 合并代码前如何审查 Copilot 智能体模式生成的更改和测试。

接下来，使用 [Playwright MCP 服务器测试功能][next-lesson]，并为其创建拉取请求。

### 加分探索练习 - 实现分页

游戏列表不断扩大后，需要支持分页。运用本练习学到的技能，让 Copilot 更新网站并实现分页。考虑以下事项：

- 遵循现有最佳实践，包括使用现有指令文件。
- 考虑分页方式，例如让用户选择每页数量，或使用固定值。
- 编写提示词时，提供 Copilot 所需的指导，确保实现符合预期。
- 可能需要与 GitHub Copilot 反复迭代、提出更改并补充上下文。这是使用 Copilot 的正常方式。

## 资源

- [Copilot 的 ask、edit 和 agent 模式：功能及使用时机][choose-mode]
- [VS Code 中的智能体模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
