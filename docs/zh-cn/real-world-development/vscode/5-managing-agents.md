---
title: "练习 5 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义智能体][previous-lesson] |
|:--|

使用 GitHub Copilot 智能体模式时，它会自主探索代码库、提出更改、编辑文件并运行命令。由于工作会在本机实时进行，VS Code 中的 Copilot Chat 会持续显示每次工具调用和文件编辑。你可以直接在聊天中查看差异、逐项接受或拒绝更改，并通过后续提示词引导对话、完善或扩展工作，无需离开编辑器。

本练习将：

- 实时监控无障碍智能体的工作。
- 审查并接受建议的文件编辑。
- 在会话进行中引导智能体添加浅色模式切换，同时保留高对比度模式。

## 场景

你在[练习 4][previous-lesson]中使用的无障碍自定义智能体正在实现高对比度模式。工作进行期间，设计团队又提出了新需求：为觉得默认主题过暗的用户添加浅色模式切换。无需从头开始新对话，你可以引导现有会话扩展工作范围。

## 监控 Copilot 进度

Copilot Chat 视图会显示智能体执行的每个步骤，包括探索的文件、运行的命令和提出的编辑。每项文件编辑都会以内嵌差异和 **Keep**/**Undo** 控件显示。

1. 从练习 4 返回 Copilot Chat 视图。
2. 随着智能体工作，滚动聊天记录查看每次工具调用，包括文件读取、文件编辑和需要运行的终端命令。
3. 智能体提出文件编辑时，差异会以内嵌方式显示：
   - 选择 **Keep** 接受更改。
   - 选择 **Undo** 撤销更改，让智能体重新尝试。
4. 也可以打开聊天顶部的 **Files changed** 指示器，查看此会话中智能体修改的完整文件列表。

> [!NOTE]
> 如果工具调用需要权限（例如运行终端命令），智能体会暂停并显示 **Allow** 提示。只批准你信任的命令。

5. 等待智能体完成高对比度模式。它会显示总结所做更改的最终消息。

## 引导智能体添加浅色模式

高对比度模式完成后，在同一对话中继续添加浅色模式切换。由于延续同一聊天会话，智能体会保留此前工作的上下文，包括修改的组件、采用的样式方案和本地存储模式。

1. 在 Copilot Chat 输入框中发送以下后续提示词：

   ```plaintext
   Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
   ```

2. 智能体会从之前的进度继续，检查已有更改并提出浅色模式相关修改。
3. 像审查高对比度模式一样，逐项审查新编辑，并使用 **Keep** 或 **Undo**。
4. 如果智能体偏离预期（例如尝试以不喜欢的方式重构切换 UI），可以使用聊天输入框旁的 **Stop** 按钮停止，然后发送说明更正方向。

> [!TIP]
> 引导正在进行的会话比重新开始更快。可以通过后续提示词提出调整（“简化 CSS，改用 CSS 变量，避免重复选择器”）、纠正方向（“不要添加第三个切换控件，把浅色模式整合到现有设置面板中”）或扩展范围（“为两个切换控件添加键盘快捷键”）。

## 审查工作集

提交工作之前，快速检查智能体修改的所有内容。

1. 在 VS Code 中打开 **Source Control** 视图。
2. 查看所有更改文件。应能看到 Astro 组件、样式和相关测试的更新。
3. 打开部分更改文件并查看差异。确认符合自定义智能体的无障碍模式，包括 ARIA 属性、键盘导航、语义化 HTML，以及通过本地存储持久保存设置。
4. 确认满意后，在 Source Control 面板中暂存并提交更改。下一课会发布这些更改。

## 总结与后续步骤

你已使用 Copilot Chat 智能体模式监控并引导进行中的会话：逐项审查文件编辑、接受或拒绝更改，并在保留上下文的情况下通过后续提示词扩展工作。

本课中，你了解了：

- 实时监控智能体的工作。
- 接受和拒绝单个文件编辑。
- 使用后续提示词在会话进行中引导智能体。

在[下一课][next-lesson]中，将无障碍工作发布为拉取请求，并审查本实验中本地自定义智能体完成的更改。

## 资源

- [VS Code 中的 Copilot Chat][copilot-chat-vscode]
- [使用智能体模式][agent-mode]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
