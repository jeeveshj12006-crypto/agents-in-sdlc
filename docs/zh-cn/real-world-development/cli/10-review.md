---
title: "第 10 课 - 总结与后续步骤"
description: "回顾 Copilot CLI 工作流、两个拉取请求、可复用自定义项和后续资源。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

你已在 Tailspin Toys 的完整工作流中使用 GitHub Copilot CLI。你：

- 准备 Codespace、安装 Copilot CLI、探索项目，并找到已创建的筛选议题。
- 添加星级评分，在转发的浏览器中审查结果，并手动合并第一个拉取请求 (PR)。
- 从筛选议题开始，在 Plan 模式中确定方案，在 Autopilot 模式中实现，并在 Interactive 模式中审查。
- 使用自定义指令引导智能体，自定义现有 `quality-checks` 技能，并用它运行单元测试、lint 和类型检查。
- 添加 Playwright Model Context Protocol (MCP) 服务器，并在真实浏览器中体验筛选功能。
- 创建并选择质量保证 (QA) 自定义智能体，以评估需求、覆盖率、技能结果和浏览器证据。
- 审查完整的筛选功能更改，并授权 Agent Merge 合并筛选 PR。
- 探索用于管理上下文、模型、共享和可选云端委派的斜杠命令。

## 交付内容

本研讨会包含两个 PR 里程碑，分别从更新后的 `main` 创建分支：

1. **星级评分：** 在游戏卡片上显示现有 `starRating` 和明确的未评分状态。
2. **筛选与质量工作流：** 实现筛选功能，更新指令并应用于该功能，自定义 `quality-checks` 报告，创建 QA 配置文件，并包含相关测试。

从规划筛选功能到创建 PR，你一直使用同一对话和分支。为简化研讨会，我们将所有工作合并到一个 PR。

## 多种验证方式

你通过多种方式检查代码：自动化测试、亲自在浏览器中检查，以及通过 MCP 让 Copilot 探索浏览器。`quality-checks` 技能运行单元测试、lint 和类型检查，并按新的格式报告结果。QA 汇总了这些结果，并在 PR 创建前审查需求和测试覆盖率。

添加的测试应弥补真实缺口；如果 QA 运行后认为无需新增测试，也可能是正确的判断。授权合并前，请审查代码和证据。更改后，应更新受影响的验证证据。

## 最佳实践

提供给 Copilot 的上下文和工具会影响它的工作。本研讨会中，你更新了指令、自定义了技能、创建了 QA 配置文件并配置了 MCP 服务器。可以在不同对话中复用这些自定义项，并根据团队需求调整。指令设定规范，技能描述可重复任务，自定义智能体定义专家角色，MCP 服务器连接外部工具。审查实际更改和工具结果，而不要只看智能体的总结。

根据任务选择合适的**模式和模型**。使用 **Plan** 在实现前思考方案，使用 **Interactive** 参与专注的更改，使用 **Autopilot** 处理范围明确的任务。日常编辑可选择较快的模型，复杂工作则使用能力更强的模型。

上下文和基础设施同样重要。清楚说明要构建*什么*、*为什么*以及*如何*实现，会明显影响结果。

## 继续探索

你已了解核心工作流。还可以尝试以下 CLI 功能：

- `/review`：让代码审查智能体分析更改。
- `/rubber-duck`：讨论问题并获得另一种观点。
- `/fleet`：并行协调独立子任务。
- `/worktree`：隔离单独任务。
- `/delegate`：将任务交给 Copilot 云智能体。

## 后续步骤

持续使用工具是提升能力的最佳方式。将它用于生产代码、个人项目，或用于实现那个想了多年却一直没时间开发的小应用。与团队分享经验，也向同事学习。并且一如既往，继续探索文档。

若要从终端扩展 Tailspin Toys，可继续学习可选的 [Foundry Backer Concierge 系列课程][foundry]。要比较不同环境，请探索 [VS Code 研讨会][vscode]、[GitHub Copilot app 研讨会][app]或 [Copilot 云智能体研讨会][cloud]。

## 资源

- [关于 GitHub Copilot CLI][about-cli]
- [Copilot CLI 命令参考][cli-reference]
- [自定义 Copilot CLI][customize-cli]
- [使用 Copilot CLI 管理拉取请求][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
