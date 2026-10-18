---
title: "练习 4 - 自定义智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## 什么是自定义智能体？

GitHub Copilot 中的[自定义智能体][custom-agents-concept]可针对开发工作流中的特定任务或领域创建专用 AI 助手。在存储库的 `.github/agents` 文件夹中通过 Markdown 文件定义智能体，可以为 Copilot 提供专门指令、最佳实践、编码模式和领域知识，引导它更有效地完成特定类型的工作。团队可以将专业知识整理为可复用智能体，例如执行 [WCAG][wcag] 合规检查的无障碍智能体、遵循安全编码实践的安全智能体，或维护一致测试模式的测试智能体。

自定义智能体可以通过项目 `.github/agents` 文件夹中的 Markdown 文件定义，也可以全局定义在 `~/.copilot/agents` 中。每个文件都包含 YAML frontmatter，至少要有 `name` 和 `description`，后面是说明智能体行为、专业领域和指令的 Markdown 提示。

### 自定义智能体与智能体技能的区别

自定义智能体和[智能体技能][agent-skills-concept]有一些相似之处。两者主要通过 Markdown 文件定义，并告诉 AI 如何执行操作。最简单的区分方式是：**自定义智能体**是工作者，**技能**是工具。

自定义智能体拥有自己的上下文窗口，并用于编排技能（甚至其他智能体）以完成工作。在本实验中，无障碍自定义智能体会根据无障碍指南审查并更新网站；执行过程中，它可以调用拉取请求工作流技能或测试运行及管理技能。

> [!NOTE]
> 编写自定义智能体没有唯一的“正确”方法。和其他 AI 用法一样，需要通过测试和迭代找到适合环境和场景的方式。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

## 场景

Tailspin Toys 致力于让所有用户都能访问其众筹平台，无论用户的视觉能力或偏好如何。近期用户反馈指出，部分用户认为当前深色主题难以阅读，因为文本与背景颜色的对比度不足。为解决这一无障碍问题，设计团队要求实现可由用户切换的高对比度模式。

无障碍功能至关重要，因此希望尽快实现。接下来使用自定义智能体生成该功能。

本练习将：

- 查看现有无障碍自定义智能体。
- 在 Copilot Chat 中使用该智能体实现高对比度模式。

## 查看无障碍自定义智能体

项目已为无障碍工作创建自定义智能体。先查看其内容，了解它将如何引导 Copilot。

返回 codespace，在无障碍工作中从 `main` 创建一个新分支（这样可以将筛选 PR 与练习 3 中的工作分开）：

```bash
git checkout main
git pull
git checkout -b accessibility-vscode
```

1. 打开 `.github/agents/accessibility.md`。
2. 查看包含 `name` 和 `description` 字段的 YAML frontmatter。

> [!CAUTION]
> 自定义智能体必须包含 `name` 和 `description` frontmatter。

3. 扫读后续部分，了解其中列出的：
   - 为无障碍网站生成代码时的核心职责。
   - 无障碍最佳实践。
   - HTML、CSS 和 JavaScript 示例。
   - 常见问题和错误。

## 在 Copilot Chat 中使用自定义智能体

VS Code 会将 `.github/agents` 中定义的所有自定义智能体显示在 Copilot Chat 视图底部的智能体下拉菜单中。选择自定义智能体后，聊天会话将依据该智能体的指令和工具开展。

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始干净的新对话。模式和模型选择会因练习而异，相关步骤会注明。

1. 如果 Chat 视图的智能体下拉菜单中尚未选中 **Agent**，请先选择它。

   ![Chat 视图中智能体选择器的截图。](../../../_images/shared-chat-mode-selector.png)

2. 选择聊天视图底部的智能体下拉菜单（默认显示当前智能体 **default**）。
3. 从可用智能体列表中选择 **Accessibility agent**。
4. 向无障碍智能体发送以下提示词：

   ```plaintext
   Add a high-contrast mode to the site. There should be a toggle for high contrast which the user can set, and the setting should persist across page reloads using local storage on the browser.
   ```

5. Copilot Chat 会探索代码库、提出更改并编辑项目文件。每项编辑都会在聊天中显示文件路径和差异，供你审查。
6. 随着编辑进行，**Files changed** 指示器会更新，显示智能体修改的文件。

> [!NOTE]
> 此过程可能需要几分钟。Copilot 会实际修改存储库，例如更新 Astro 组件、CSS 和相关测试。

下一练习会审查并引导这项进行中的工作。

## 总结与后续步骤

本课探索了 GitHub Copilot 中的[自定义智能体][custom-agents]，即针对特定任务和领域设计的专用 AI 助手。借助自定义智能体，可以将团队专业知识和标准整理为可复用智能体，更有效地引导 Copilot 完成工作。

你了解了：

- 自定义智能体如何定义。
- 如何在 Copilot Chat 智能体模式中使用自定义智能体。

接下来，[监控并引导智能体工作][next-lesson]，实时审查更改，并在同一会话中添加浅色模式切换。

## 资源

- [关于自定义智能体][custom-agents]
- [在 IDE 中创建自定义智能体][creating-custom-agents-ide]
- [VS Code 中的自定义智能体][custom-agents-vscode]
- [自定义智能体配置][custom-agents-config]
- [awesome-copilot 上的自定义智能体][awesome-copilot-agents]

---

| [← 上一课：使用 Playwright MCP 服务器测试功能][previous-lesson] | [下一课：监控和管理智能体 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-mcp/
[next-lesson]: ../5-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[creating-custom-agents-ide]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide
[custom-agents-vscode]: https://code.visualstudio.com/docs/copilot/customization/custom-agents
[custom-agents-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
[awesome-copilot-agents]: https://github.com/github/awesome-copilot/tree/main/agents
