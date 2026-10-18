---
title: "レッスン 6 - Playwright MCP による機能の検証"
description: "Copilot CLI で Playwright MCP を確認または構成し、ブラウザーでフィルター機能を試します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

コードを書くこと以外にも、データや外部サービスを扱い、Copilot が利用できる自動化を追加する必要があります。MCP サーバーを使うと、Copilot CLI に組み込まれている機能だけでなく、ほかのツールやサービスも利用できます。

このレッスンでは、次の内容を学習します。

- Model Context Protocol (MCP) と Copilot CLI での利用方法を理解します。
- 使用できない場合は Playwright MCP サーバーを追加します。
- エージェントにブラウザーを操作させ、フィルター機能を確認します。

## シナリオ

単体テストと E2E テストは重要ですが、UI の更新を検証するには実際に操作する必要があります。Copilot がユーザーのようにサイトを操作できるようにして、変更が想定どおりに動くことをより確実にします。

## Model Context Protocol (MCP) とは

[Model Context Protocol (MCP)][mcp-blog-post] を使うと、AI エージェントが外部ツールやサービスと通信できます。MCP によって、エージェントは最新情報を参照し、ユーザーの代わりに操作を実行できます。

ツールやリソースへのアクセスは、AI エージェントと外部ツール・サービスをつなぐ MCP サーバーを通じて行います。各 MCP サーバーは、エージェントが利用できるツールとリソースのセットを提供します。

よく使われる MCP サーバーには、次のものがあります。

- **[GitHub MCP Server][github-mcp]**: GitHub リポジトリ、Issue、プルリクエストを管理する API にアクセスできます。
- **[Playwright MCP Server][playwright-mcp-server]**: Playwright を使ったブラウザー自動化機能を提供します。

ほかにも多くの MCP サーバーがあります。GitHub はエコシステム内のサーバーを見つけやすくし、コントリビューションを促す [MCP レジストリ][mcp-registry]をホストしています。

> [!CAUTION]
> MCP サーバーはプロジェクトのほかの依存関係と同じように扱ってください。使用前にソースコードを確認し、発行元を検証し、セキュリティへの影響を検討してください。

## Playwright MCP サーバーを追加する

Playwright MCP サーバーがまだ使えない場合は、Copilot がユーザーのようにサイトを操作できるように追加します。

> [!NOTE]
> Copilot CLI に Playwright MCP がすでに構成されている場合は、セットアップを省略して次のセクションに進みます。

1. Codespace に戻ります。
2. Copilot CLI で次のコマンドを実行し、MCP サーバーの追加ダイアログを開きます。

   ```plaintext
   /mcp add
   ```

3. 名前に `playwright` と入力して、<kbd>Tab</kbd> を押します。
4. <kbd>Enter</kbd> を押してサーバーの種類を **STDIO** にし、<kbd>Tab</kbd> を押します。
5. **Command** ダイアログに次の内容を貼り付けます。

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. <kbd>Control</kbd>+<kbd>S</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>S</kbd> (Windows/Linux) を押して新しい MCP サーバーを保存します。
7. <kbd>Esc</kbd> を押して MCP ダイアログを閉じます。

## Playwright で機能を確認するよう Copilot に依頼する

これまでは機能の動作を手動で確認しました。次は Copilot が利用できる Playwright MCP サーバーで同じ確認を行います。

1. 次のプロンプトを使って、Playwright MCP サーバーによる検証を依頼します。

   ```plaintext
   Start the app and use Playwright MCP to check filtering against the issue and our plan. Tell me what works and what doesn't, without making changes. Stop the server you started when you're done.
   ```

> [!NOTE]
> 通常、Copilot は MCP サーバーを自動的に見つけるため、明示的に指定する必要はありません。ただし、利用するツールが分かっている場合は、方向を示すことで一貫した結果を得やすくなります。

2. Copilot がブラウザーで操作し、機能を確認する様子を見ます。
3. レポートを読み、すべて想定どおりに動作することを確認します。

Copilot はサーバーを起動し、Playwright でサイトを操作した後、サーバーを停止して結果を報告します。

## まとめと次のステップ

Copilot CLI から Playwright MCP サーバーを使い、実際のブラウザーで機能を確認しました。このレッスンでは、次の内容を学びました。

- Model Context Protocol (MCP) と Copilot CLI での利用方法を確認しました。
- 必要に応じて Playwright MCP サーバーを追加しました。
- エージェントにブラウザーを操作させ、フィルター機能を確認しました。

次は、[スキルとブラウザーツールを組み合わせる QA カスタムエージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ注目されているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [Copilot CLI に MCP サーバーを追加する][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
