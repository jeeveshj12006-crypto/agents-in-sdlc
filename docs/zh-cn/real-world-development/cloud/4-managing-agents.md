---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

在最近几次练习中，你要求 Copilot 云智能体完成了三项改善用户体验和增加功能的任务。云智能体虽然可以异步自主工作，但监控这些任务仍然很重要。

你可以使用多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个任务控制中心中，可以查看所有拥有未关闭拉取请求 (PR) 的智能体任务，探索已执行的操作，甚至引导正在进行的会话。

本课将：

- 探索智能体页面，监控云智能体任务。
- 在会话进行中引导智能体添加功能。

## 场景

将高对比度模式任务分配给智能体后，团队认为也应趁此机会添加浅色模式。既然已经在更新网站样式并添加切换功能，一并加入此功能很合理。现在引导智能体，确保除了高对比度模式之外也实现浅色模式。

## 查看 Copilot 云智能体任务

查看当前分配给 Copilot 云智能体的所有任务状态。

1. 转到 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 查看主面板和左侧面板中的任务列表。应能看到已分配给 Copilot 的任务，包括：
   - 更新代码库文档。
   - 生成修改游戏的 API。
   - 为网站添加高对比度模式。
3. 选择其中一个正在运行的任务，查看 Copilot 已执行的操作，例如：
   - 检出存储库代码。
   - 创建 Copilot 工作环境。
   - 设置 MCP 服务器。
   - 执行完成分配任务所需的其他步骤。

> [!NOTE]
> 列出的具体步骤取决于 Copilot 的工作状态和所采用的方法。

4. 还要查看右侧显示的拉取请求 (PR) 面板。它可以显示 PR 和已更改文件，方便进一步监控。

## 引导云智能体

了解当前正在执行的任务后，让 Copilot 在处理高对比度模式时也添加浅色模式切换。

1. 选择标题与添加高对比度模式相关的会话。具体标题会根据 Copilot 使用的名称和工作状态而异。

   ![任务控制中心中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 观察几分钟，直到会话显示已完成设置并开始工作。届时会看到类似下方的消息。
3. 在 **Steer active session while Copilot is working** 对话框中添加以下提示词：

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![智能体页面中高亮显示 Steer active session while Copilot is working 对话框的云智能体任务截图](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示词。
5. 注意 Copilot 如何确认收到提示并将其纳入工作流程。

## 让 Copilot 继续工作

和之前一样，Copilot 会继续处理更新后的任务！它会在完成收到消息时正在执行的具体步骤后，将新需求纳入工作流程。

此过程同样需要几分钟，期间可以暂停并回顾目前所学内容。

## 总结与后续步骤

本课探索了 Copilot 智能体页面，这是监控和引导 GitHub Copilot 云智能体任务的中心位置。通过此任务控制中心，可以跟踪所有进行中和已完成的任务、审查当前工作，甚至重新引导正在运行的会话以调整范围或补充指导。

你了解了：

- 使用任务控制中心和智能体页面监控云智能体任务。
- 在会话进行中重新引导智能体，要求添加功能。

Copilot 正在完成无障碍功能，接下来可以[迭代改进 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
