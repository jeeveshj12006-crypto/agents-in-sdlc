---
title: "演習 2 - Copilot Agent Mode で機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションへの最小限の更新でも、複数のファイルを変更し、テストなどを実行する必要があります。開発者は通常、必要なファイルを探し、変更し、テストを実行して、デバッグし、見落としたファイルを特定して修正するという流れを繰り返します。

このような場面で Copilot Agent Mode が役立ちます。

Copilot Agent Mode は IDE でより自律的に作業します。開発者と同じように、既存のプロジェクト構成を調査し、必要な変更を行い、テストを実行し、見つかった問題を自動で修正します。Agent Mode でサイトに新機能を追加する方法を確認しましょう。

> [!NOTE]
> 名前は似ていますが、エージェントモードと cloud agent は異なる体験を提供します。エージェントモードは IDE 内で動作するため、すばやくフィードバックを得て対話できます。Cloud agent はチームメンバーのように非同期で作業し、Issue とプルリクエストを通じてやり取りします。

この演習では、次の内容を学習します。

- Copilot Agent Mode がプロジェクトを調査し、関連ファイルを見つけ、複数の変更を調整する方法を確認します。
- UI とデータレイヤーにまたがる機能を実装します。
- Copilot Agent Mode が生成した変更とテストをレビューしてからマージします。

## シナリオ

ゲーム一覧が増えているため、ユーザーがカテゴリーと発行元で絞り込めるようにします。前の演習で発行元ヘルパーを追加しました。残りのデータレイヤー、UI、テストの作業を Copilot Agent Mode で完了します。

## Tailspin Toys の Web サイトを実行する

変更を行う前に、Tailspin Toys の現在の機能を確認します。

この Web サイトは、開発者をテーマにしたボードゲーム向けのクラウドファンディングプラットフォームです。ゲーム一覧と詳細を表示できます。単一の Astro アプリで、ビルド時にページを静的 HTML として生成します。各ページの frontmatter から Drizzle ORM を通じてローカル SQLite データベースを直接参照します。別のバックエンド API やクライアント側 UI フレームワークはありません。再利用可能なデータアクセスヘルパーは `src/lib/` にあり、インタラクティブな動作は標準 DOM API を使う小さな Astro `<script>` で実装します。

### Web サイトを起動する

単一 Astro 開発サーバーを起動する `npm run dev` タスクを使用します。

1. Codespace に戻ります。前の演習を終えて `main` にいることを確認します。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して Codespace 内に新しいターミナルを開きます。
3. `main` を更新して、フィルター機能用のブランチを作成します。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドで Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL が表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くブラウザーウィンドウのダイアログが表示された場合は、**x** を選択して閉じます。

5. <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら、ターミナルの Astro サーバーアドレス `http://localhost:4321` を選択して Web サイトを開きます。

> [!NOTE]
> Codespace ターミナルから localhost URL を選択すると、`https://<your-codespace-name>-4321.app.github.dev/` に自動的に転送されます。これは Web サーバーをホストする Codespace へのプライベートトンネルです。

### Web サイトを確認する

Web サイトを起動したら、次の機能を確認できます。

- **ホームページ**: タイトル、画像、説明を含むボードゲームの一覧を表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、発行元、カテゴリーなどの情報を表示します。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 次の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習ごとに異なります。必要な場合は手順で説明します。

テンプレートからプロジェクトを設定すると、Issue のバックログが自動作成されています。GitHub Copilot に項目を確認してもらいます。

1. Chat ビューのエージェント一覧で **Agent** を選択します。Agent はファイルをまたいで変更を計画・実装し、ターミナルコマンドを実行してツールを呼び出します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデルから **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が特定のモデルを推奨しているわけではありません。作成時に Claude Sonnet 4.5 を使ったため、手順に含めています。コード提案を比較的そろえて学習しやすくするためですが、LLM は確率的なため、例と異なる提案になることがあります。これは想定された動作です。

> [!NOTE]
> LLM の確率的な性質により、Copilot は異なる MCP コマンドを使う場合がありますが、タスクは完了できるはずです。

3. 次のプロンプトでバックログの Issue を確認します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択してすべての Issue を一覧表示します。
5. 生成された一覧を確認します。

Copilot が、ユーザーにとって役立つと思われる順に項目を優先付けします。

## 指示ファイルを確認する

コード生成を始める前に、Copilot にコンテキストを与える指示ファイルを確認します。Web サイトに機能を追加する方法が書かれた[ユーザーインターフェイス (UI) 指示][ui-instructions]を使います。

1. Codespace で `.github/instructions/ui.instructions.md` を開きます。
2. 機能追加に関する全体的な指針を確認します。アーキテクチャの概要、コンポーネント設計、テスト容易性とアクセシビリティの原則、Astro、Tailwind CSS、Drizzle などのファイル別指示へのリンクが含まれます。

> [!TIP]
> 指示ファイルから、ほかの指示ファイルやプロジェクト内のファイルを参照できます。パスは指示ファイルからの相対パスです。指示の再利用、複雑な内容の分割、Copilot への例やテンプレートの提示に役立ちます。

## フィルター機能を実装する

フィルター機能を完成させるには、少なくとも次の 3 つを更新します。

- データレイヤー (`src/lib/`) に残りのフィルター処理を追加または改善します。
- フィルター動作のテストを追加または更新します。
- ゲーム一覧ページにフィルター UI を追加します。

さらに、コードをマージする前にテストを実行し、合格させる必要があります。Copilot Agent Mode にこれらの作業を依頼します。

1. 現在の会話を続けるか、**New Chat** を選択して新しい会話を始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択し、指示ファイルを追加します。

   ![UI 指示ファイルを選択する例](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェント一覧で **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. モデルに **Claude Sonnet 4.5** が選択されていることを確認します。
5. バックログの関連 Issue に基づいて実装するよう、次のプロンプトを送信します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調査し、機能に関連するファイルを特定する様子を確認します。データレイヤーのヘルパー、UI、テストを見つけてファイルを変更し、テストを実行します。

   ![Copilot がプロジェクトファイルを調査する画面](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、変更、テストを行います。タスクやコードベースの複雑さによって数分かかることがあります。作業中はエディターに **Keep** と **Undo** が表示される場合があります。処理中に選択する必要はなく、完了後にすべての変更をまとめて保持または取り消せます。

7. Copilot に求められたら **Continue** を選択してテストを実行します。

   ![テスト実行の確認を求める Copilot Chat のダイアログ](../../../_images/ex3-agent-mode-run-tests.png)

8. 途中で待ち時間が発生したりテストが失敗したりする場合があります。Copilot はコード生成とテストを繰り返し、エラーがないと判断するまで作業します。

   ![Copilot Agent Mode の完了したチャットセッション](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot やほかの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを表示しているブラウザーに戻り、新機能を試します。
11. 動作を確認してコードをレビューしたら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

GitHub Copilot Agent Mode で Tailspin Toys の Web サイトに機能を追加する方法を確認しました。この演習では、次の内容を学びました。

- GitHub Copilot Agent Mode で UI とデータレイヤーに機能を実装する。
- Copilot Agent Mode でプロジェクトを調査し、関連ファイルを特定して変更を調整する。
- コードベースにマージする前に、生成された変更とテストをレビューする。

次は、[Playwright MCP サーバーで機能をテストし][next-lesson]、プルリクエストを開きます。

### ボーナス演習 — ページネーションを実装する

ゲーム一覧が増えると、ページネーションが必要になります。この演習で学んだ内容を使って、Copilot にページネーションの実装を依頼します。既存の指示ファイルを使い、ページサイズをユーザーが選択するか固定にするかを検討します。希望する実装に必要な指針をプロンプトに含め、Copilot と反復して改善します。

## リソース

- [Copilot の Ask、Edit、Agent モード: それぞれの役割と使い分け][choose-mode]
- [VS Code のエージェントモード][vs-code-agent-mode]

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
