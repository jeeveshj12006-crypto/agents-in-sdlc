---
title: "第 8 课 - 创建并合并功能 PR"
description: "一起审查筛选功能、指令、技能更新、QA 配置文件和测试，然后创建 PR 并使用 Agent Merge。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

筛选功能实现、指令更新、技能更新、质量保证 (QA) 配置文件和测试都保存在同一个分支上。现在一起审查这些更改并创建拉取请求。星级评分 PR 是由你手动合并的；这次将由 **Agent Merge** 管理合并流程。

> [!NOTE]
> 通常会把功能、指令更新、技能更新和 QA 智能体拆分为几个 PR。为简化研讨会，我们在同一对话和分支中完成整个筛选及质量工作流，并将这些工作放入此 PR。

本课将：

- 了解 Agent Merge 及其如何自动处理合并生命周期。
- 检查完整功能更改和验证证据。
- 创建筛选功能 PR。
- 仅在审查完成后启用 Agent Merge，并确认 PR 已合并。

## 场景

在筛选功能工作流中，你已使用 Copilot 规划、实现并验证功能。Tailspin Toys 希望自动处理剩余的 PR 工作，同时让开发人员保留合并授权权。

## 认识 Agent Merge

**Agent Merge** 会自动处理合并拉取请求所需的后续工作。启用后，Copilot 会处理阻止 PR 合并的问题，例如修复持续集成 (CI) 检查失败、回应审查意见，以及在需要时执行变基。存储库允许时，它还会启用 GitHub 自动合并。

此前一直由你选择 **Merge pull request**。Agent Merge 可以承担这项工作，但启用之前仍应审查并确认更改已准备好合并。

## 使用 Agent Merge 管理 PR

代码都已完成，现在一起审查、创建 PR，并让 Agent Merge 管理后续流程。

1. 返回 Codespace。
2. 输入 `/agent` 打开智能体对话框。
3. 从选项中选择 **Default**，然后按 <kbd>Enter</kbd>。
4. 输入 `/pr create` 创建 PR。
5. 输入 `/pr agentmerge` 启用 Agent Merge。
6. Copilot 会监控 PR 的持续集成流程。所有检查通过后，它会执行合并。
7. 确认 Copilot 显示了类似“PR #14 was squash-merged successfully.”的消息。

> [!IMPORTANT]
> Agent Merge 不会绕过必需的审批、分支保护、合并队列、存储库设置或权限限制。如果流程受阻，请查看报告的原因，并在存储库允许的情况下手动完成审查后的合并。

## 总结与后续步骤

你已自动化开发流程中的多个环节，包括生成代码、测试和验证代码，以及处理拉取请求。本课中，你：

- 了解 Agent Merge 及其如何自动处理合并生命周期。
- 检查完整功能更改和验证证据。
- 创建筛选功能 PR。
- 在审查完成后启用 Agent Merge，并确认 PR 已合并。

接下来，[探索其他实用的 Copilot CLI 斜杠命令][next-lesson]，了解上下文、模型、共享和可选的云端委派。

## 资源

- [使用 Copilot CLI 管理拉取请求][manage-prs]
- [Copilot CLI 命令参考][cli-reference]

[previous-lesson]: ../7-qa-agent/
[next-lesson]: ../9-cli-power-tools/
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
