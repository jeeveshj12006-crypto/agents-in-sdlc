---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: エージェントモード][previous-lesson] · [次のレッスン: カスタムエージェント →][next-lesson]

エージェントモードでフィルター機能を構築しました。プルリクエストを開く前に、ブラウザーで動作を確認します。アプリを自分で操作する代わりに、**Playwright MCP サーバー**を接続し、Copilot に実際のブラウザーを操作してテストさせます。その後、ブランチを公開して PR を開きます。

この演習では、次の内容を学習します。

- Model Context Protocol (MCP) と、MCP サーバーで Copilot にツールを追加する方法を学びます。
- Playwright MCP サーバーをワークスペースに追加します。
- ブラウザーでフィルター機能を手動テストするよう Copilot に依頼します。
- ブランチを公開し、フィルター機能の PR を開きます。

## Model Context Protocol (MCP) とは

エージェントモードは、エディターの外部にアクセスできると、さらに便利になります。Model Context Protocol (MCP) は、Copilot が外部ツールやサービスと通信するための標準です。

![コンテキスト、LLM、MCP サーバーや VS Code 拡張機能のツールなどと連携するエージェントモードの図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) を使うと、AI エージェントは外部ツールやサービスとリアルタイムに通信できます。リソースから最新の情報にアクセスし、ツールを使ってユーザーの代わりに操作できます。

ツールやリソースへのアクセスは、AI エージェントと外部ツール・サービスをつなぐ MCP サーバーを通じて行います。MCP サーバーは、既存の API や npm パッケージのようなローカルツールとの通信を管理します。各サーバーは、エージェントが利用できるツールやリソースを提供します。

よく使われる MCP サーバーには、次のものがあります。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリ、Issue、プルリクエストを管理する API にアクセスできます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を提供し、Web ページの移動、フォーム入力、ボタン選択などを行えます。

ほかにもさまざまなツールやリソースにアクセスする MCP サーバーがあります。GitHub はエコシステムの発見とコントリビューションを促進する [MCP レジストリ](https://github.com/mcp)をホストしています。

> [!CAUTION]
> MCP サーバーは、プロジェクトのほかの依存関係と同じように扱ってください。使用前にソースコードを慎重に確認し、発行元を検証して、セキュリティへの影響を検討します。信頼できる MCP サーバーだけを使い、機密情報や操作へのアクセス許可には注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースから利用できる MCP サーバーを設定します。

1. Codespace で `.vscode/mcp.json` を開きます。
2. 次の `github` サーバーがすでに設定されていることを確認します。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

この `github` エントリはプロジェクトテンプレートに含まれています。そのため、前の演習で Copilot がバックログの Issue を読み取れました。[リモート GitHub MCP サーバー][remote-github-mcp-server]を使うため、ローカルにインストールする必要はありません。VS Code は HTTP で接続し、Copilot がツールを初めて使うときに GitHub で認証します。

## Playwright MCP サーバーを追加する

2 つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]は Copilot が操作できるブラウザーを提供します。これを使って機能をテストします。

1. `.vscode/mcp.json` に `github` と並べて `playwright` を追加します。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. ファイルを保存します。

リモートの `github` サーバーとは異なり、`playwright` はローカルサーバーです。VS Code は `npx @playwright/mcp@latest` を実行してサーバーを起動します。`--headless` は画面を表示せずにブラウザーを実行する設定です。デスクトップがない Codespace で必要です。

> [!NOTE]
> Tailspin Toys では E2E テストに Playwright を使っているため、通常ブラウザーはすでにインストールされています。後で Copilot からブラウザーが見つからないと報告された場合は、`npx playwright install chromium` を実行して再試行するよう依頼します。

## サーバーを起動して信頼する

VS Code は MCP サーバーを**必要なときに**起動します。Copilot が初めてサーバーのツールを使う際に、VS Code がサーバーを起動し、信頼するか確認します。

1. `.vscode/mcp.json` の `playwright` エントリの上に **Start** が表示されます。選択してサーバーを起動し、構成が正しいことを確認します。
2. VS Code がサーバーを信頼するか確認した場合は、構成をレビューして信頼できることを確認してから選択します。

手動での起動は省略できます。その場合、次の手順で Copilot がツールを初めて必要としたときにサーバーが起動し、信頼の確認が表示されます。

## フィルター機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot は実際のブラウザーを操作できます。自分でアプリを操作する代わりに、エージェントにページを開き、移動し、フィルターを適用させて、結果を読み上げてもらえます。会話を離れずに期待どおりの動作をすばやく確認できます。

Playwright MCP サーバーはスクリーンショットではなく、ページの[アクセシビリティツリー][playwright-mcp-server]を使います。エージェントはボタン、リンク、リスト項目などラベル付き要素を構造化データとして処理します。支援技術と同じ情報に基づくため、機能確認とあわせて簡単なアクセシビリティ確認も行えます。

サーバーを接続しアプリを起動した状態で、作成したフィルター機能を試すよう Copilot に依頼します。

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot は Playwright MCP サーバーでブラウザーを起動し、各手順を確認して結果を報告します。レポートを Issue の受け入れ条件と照らし合わせます。問題があれば、質問を追加するか、PR を開く前にコードの修正を依頼します。

> [!NOTE]
> このテストには `http://localhost:4321` でアプリが起動している必要があります。開発サーバーを停止した場合は、プロンプトを送信する前に起動します。Copilot が Playwright MCP サーバーを初めて使うときにブラウザーをダウンロードする場合があります。ブラウザーが見つからないと報告された場合は、`npx playwright install chromium` の実行を依頼して再試行します。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開してプルリクエストを作成する

機能が動作することを確認したので、チームがレビューできるよう PR を開きます。最初に `filtering-vscode` ブランチを公開します。

1. Codespace の **Source Control** パネルを開き、Copilot が行った変更をレビューします。
2. **+** アイコンを選択して変更をステージします。
3. **Sparkle** ボタンを使ってコミットメッセージを生成します。

   ![Copilot による変更を示す Source Control パネル](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択してブランチをリポジトリにプッシュします。

## プルリクエストを作成する

github.com や GitHub コマンドラインインターフェイス (CLI) など、PR の作成方法はいくつかあります。ここでは GitHub Copilot に作成を依頼します。関連する Issue を見つけて、PR と関連付けられます。

1. Copilot Chat パネルに移動し、**New Chat** を選択します。
2. GitHub ツールを使えるよう、エージェント一覧で **Agent** が選択されていることを確認します。
3. 次のプロンプトで PR の作成を依頼します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 情報の収集や操作に必要な場合は **Continue** を選択します。GitHub ツールを初めて使う際に認証が求められた場合は、画面の指示に従って許可します。
5. Copilot が Issue を調べ、適切なものを見つけて PR を作成することを確認します。
6. Copilot が作成したリンクから PR を確認しますが、**まだマージしないでください**。

## まとめと次のステップ

この演習では、次の内容を学びました。

- Model Context Protocol (MCP) と、MCP サーバーで Copilot にツールを追加する方法を確認しました。
- Playwright MCP サーバーをワークスペースに追加しました。
- リリース前にブラウザーでフィルター機能をテストしました。
- ブランチを公開し、PR を開きました。

MCP サーバーを使って機能をテストしました。Copilot を拡張する方法は MCP だけではありません。次は、アクセシビリティレビューなどに役立つ[カスタムエージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ注目されているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
