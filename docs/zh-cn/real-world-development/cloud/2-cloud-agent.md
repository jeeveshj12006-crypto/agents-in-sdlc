---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义指令][previous-lesson] |
|:--|

几乎所有组织都在应对技术债务，例如尚未解决的安全问题、需要更新的旧代码，或长期搁置的待办功能请求。GitHub Copilot 云智能体可以自主执行更新代码和添加功能等任务。工作完成后，它会生成草稿拉取请求 (PR)，供开发人员审查。这样可以把繁琐任务委派出去，加快开发进程，让开发人员专注于更重要的工作。

本练习将使用 Copilot 云智能体：

- 自定义代码生成环境。
- 确保操作以安全方式执行。
- 了解明确限定议题范围的重要性。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些技术债务需要处理。最初受聘开发网站的承包团队没有妥善编写文档，实际上完全缺失。团队希望首先为应用程序中所有导出的函数添加 TSDoc 文档注释。

此外，设计团队准备改进游戏发现体验。他们希望每款游戏的详情页都显示相关游戏，即同类别的其他游戏，方便用户继续浏览。他们暂时不需要完善的实现，只希望实现一个可用于 UX 验收测试的功能。目前这项工作受阻，但当前有优先级更高的议题。

这些都是容易被降级处理的任务，非常适合交给 Copilot 云智能体。它可以异步处理任务，让开发人员先处理其他工作，然后再回来审查 Copilot 的成果。

## 认识 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) 可以像人工开发人员一样在后台执行任务。例如，可以[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配任务后，Copilot 会创建草稿拉取请求以跟踪进度、设置环境并开始处理任务。你可以在会话进行中或完成后查看详情。准备好审查建议的解决方案时，它会在 PR 中标记你。

## 明确限定指令范围的重要性

GitHub Copilot 并没有魔法，不能让你跳过思考过程。看似简单的操作也可能包含复杂细节，因此应谨慎限定 Copilot 云智能体的任务范围，并参考[云智能体最佳实践][cloud-agent-best-practices]。把它当作 AI 结对程序员：分阶段工作、学习、实验并逐步调整。加入生成式 AI 并不会改变软件开发的基本原则。

## 此存储库中的自定义指令

之前的练习介绍了自定义指令及其如何引导 Copilot。如果已经完成自定义指令练习，你会看到 `.github/copilot-instructions.md` 的实际效果；如果还没有，现在可以快速查看。将工作分配给 Copilot 云智能体之前，先只读查看存储库中已有的指令文件，以便之后观察其影响。

在存储库的 GitHub 网页界面或已启动的 codespace 中打开以下文件：

- `.github/copilot-instructions.md` — 查看 **Code standards** 部分，重点关注 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md` — 注意 `applyTo` frontmatter，它将这些指令限定为 `**/*.test.ts` 文件。

下一节将 `Code lacks documentation` 议题分配给云智能体时，请留意生成的 PR 中的 TSDoc 文档注释、TypeScript 约定和注释头，这些内容来自上述指令文件。稍后练习审查 PR 时还会再次提到。

## 为 Copilot 云智能体设置开发环境

无论由谁编写代码，通常都需要特定环境和设置脚本，以确保环境状态良好。将任务分配给 Copilot 也是如此，它的工作方式类似软件工程师 (SWE)。

云智能体使用 [GitHub Actions][github-actions] 作为工作环境。可以通过 `.github/workflows/copilot-setup-steps.yml` 中配置的[专用设置工作流][setup-workflow]自定义环境，在 Copilot 开始工作前安装所需工具和依赖项。实验项目已预先配置此工作流，以便顺利完成练习并提供学习机会。它会为 Copilot 安装 Node.js、项目依赖项、端到端测试所需的浏览器二进制文件，以及单个 Astro 应用使用的迁移并已填充数据的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

它与其他 GitHub 工作流类似，但有几个要点：

- 其中只有一个名为 `copilot-setup-steps` 的作业。在 Copilot 开始处理拉取请求之前，会先在 GitHub Actions 中运行此作业。
- 注意 `workflow_dispatch` 触发器。它允许你在存储库的 **Actions** 选项卡中手动运行工作流，方便测试设置是否成功，而无需等待 Copilot 启动。

## 添加文档

所有人都知道文档很重要，但许多项目的信息已经过时或完全缺失。这类技术债务经常得不到处理，会降低效率，并增加维护代码库或让新开发人员加入团队的难度。Copilot 擅长生成文档，因此这是适合分配给 Copilot 云智能体的议题。它会在后台生成所需文档，之后的练习会审查结果。

1. 在新的浏览器选项卡中访问 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 创建空白议题。
5. 将 **Title** 设置为 `Code lacks documentation`。
6. 将 **Description** 设置为：

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。
10. 选择 **Pull Requests** 选项卡。
11. 打开新生成的拉取请求 (PR)，其标题类似 `[WIP]: Code lacks documentation`。如果列表中尚未出现新 PR，请稍等片刻并刷新浏览器。
12. 几分钟后，Copilot 应该会创建待办任务列表。

> [!NOTE]
> Copilot 的待办任务列表可能需要几分钟才会显示在 PR 中。Copilot 正在创建环境（运行前面介绍的工作流）、分析项目并确定解决问题的最佳方案。

13. 审查列表及其计划执行的任务。
14. 向下滚动 PR 时间线，应会看到 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

   ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到更新后的状态指示器。

16. 查看实时会话，了解 Copilot 如何解决问题，包括探索代码、了解当前状态、暂停思考并决定合适方案，以及编写代码。

这可能需要几分钟。Copilot 云智能体的主要目标之一是异步执行任务，让我们能够专注于其他工作。现在利用这一能力，再分配另一个任务给 Copilot 云智能体，然后转而为应用编写新功能。

## 在游戏详情页添加相关游戏部分

如前所述，GitHub Copilot 云智能体的一大优势是可以拆分工作：你处理一组任务，它同时处理另一组任务。为设计团队添加相关游戏部分可能并不复杂，但仍会占用时间。现在将它分配给云智能体。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 使用空白模板。
5. 将 **Title** 设置为 `Show related games on the game details page`。
6. 将 **Description** 设置为：

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

稍后会看到议题的第一条评论上出现一组 👀，表示 Copilot 已开始处理。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 现在正在处理第二项请求！云智能体的工作方式与软件工程师类似，因此无需主动监控，完成后再审查即可。接下来开始了解如何创建和使用自定义智能体。

## 总结与后续步骤

本课探索了 [GitHub Copilot 云智能体][copilot-agents]。可以将议题分配给云智能体，让它异步处理任务。Copilot 可以帮助解决技术债务、开发新功能，或协助将代码从一个框架迁移到另一个框架。

你了解了：

- 自定义代码生成环境。
- 确保操作以安全方式执行。
- 明确限定议题范围的重要性。
- 将议题分配给 Copilot。

云智能体正在后台工作，现在可以转向创建和使用自定义智能体。[Copilot 云智能体也可以使用 MCP 服务器][cloud-agent-mcp]，并且可以读取前面课程中探索过的自定义指令。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
