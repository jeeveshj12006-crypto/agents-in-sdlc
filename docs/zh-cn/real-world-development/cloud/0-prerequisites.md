---
title: "练习 0：先决条件"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

开始 Copilot 云智能体练习之前，需要先做好准备。你将创建自己的 Tailspin Toys 存储库副本，并启动一个 [codespace][codespaces]，用于编辑指令文件和审查云智能体生成的工作。

## 设置实验存储库

若要为练习创建代码副本，请从[模板存储库][template-repository]创建一个实例。新实例会包含实验所需的全部文件，后续练习都会使用它。

1. 在新的浏览器窗口中，转到 [Tailspin Toys 模板][tailspin-template]。
2. 在实验存储库页面上选择 **Use this template** 按钮，然后选择 **Create a new repository**，创建自己的存储库副本。

    ![Use this template 按钮](../../../_images/ex0-use-template.png)

3. 如果你是在 GitHub 或 Microsoft 举办的活动中参加本研讨会，请遵循导师提供的说明。否则，可以在你有权使用 GitHub Copilot 的组织中创建存储库。

    ![输入存储库模板设置](../../../_images/ex0-repository-settings.png)

4. 记下创建的存储库路径（**organization-or-user-name/repository-name**），后续练习会用到。

## 创建 codespace

接下来，使用 codespace 完成实验练习。

[GitHub Codespaces][codespaces] 是一种基于云的开发环境，可让你直接在浏览器中编写、运行和调试代码。它提供功能齐全的 IDE，并支持多种编程语言、扩展和工具。

1. 转到刚创建的存储库。
2. 选择绿色的 **Code** 按钮。

    ![选择 Code 按钮](../../../_images/ex0-code-button.png)

3. 选择 **Codespaces** 选项卡，然后选择 **+** 按钮创建新的 Codespace。

    ![创建新的 codespace](../../../_images/ex0-create-codespace.png)

创建 codespace 需要几分钟，不过这仍比手动安装所有服务快得多。等待期间，可以探索 GitHub Copilot 的其他功能；接下来我们会介绍这些功能。

> [!CAUTION]
> 后续练习还会用到此 codespace。现在先让它保持打开，并留在浏览器选项卡中。

> [!NOTE]
> 本研讨会可在 codespace 或本地 [dev container][dev-containers] 中完成。这两种方式都会确保环境已安装所需的先决条件，从而顺利开展练习。如果希望在本地运行，请在 VS Code 中打开克隆的存储库；出现提示时选择 **Reopen in Container**，VS Code 会构建 codespace 使用的同一 dev container。

[codespaces]: https://github.com/features/codespaces
[dev-containers]: https://code.visualstudio.com/docs/devcontainers/containers

## 总结

恭喜，你已创建实验存储库的副本，并开始创建 codespace。后续练习会使用此环境配合 Copilot 云智能体开展工作。

## 下一步

接下来，为云智能体添加自定义指令。继续学习[练习 1：自定义指令][next-lesson]。

## 资源

- [GitHub Codespaces 概述][codespaces]
- [从模板创建存储库][template-repository]
- [Codespaces 入门][codespaces-quickstart]

[tailspin-template]: https://github.com/github-samples/tailspin-toys
[template-repository]: https://docs.github.com/repositories/creating-and-managing-repositories/creating-a-template-repository
[codespaces-quickstart]: https://docs.github.com/codespaces/getting-started/quickstart
[next-lesson]: ../1-custom-instructions/
