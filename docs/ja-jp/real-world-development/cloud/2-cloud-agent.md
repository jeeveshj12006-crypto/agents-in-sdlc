---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

技術的負債に悩んでいない組織はほとんどありません。未解決のセキュリティ問題、更新が必要なレガシーコード、実装する時間がなくバックログに残った機能の要望などがあります。GitHub Copilot cloud agent は、コードの更新や機能追加などのタスクを自律的に実行できます。作業が完了すると、人間の開発者がレビューできる下書き PR を作成します。これにより、時間のかかるタスクを任せ、開発を加速させ、開発者はより広い課題に集中できます。

この演習では、Copilot cloud agent を使って次の内容を確認します。

- コード生成用の環境をカスタマイズします。
- 操作が安全に行われることを確認します。
- Issue の範囲を明確にする重要性を学びます。
- Issue を Copilot に割り当てます。

## シナリオ

Tailspin Toys には対応したい技術的負債があります。サイトの初期バージョンを作った委託業者は、ドキュメントを十分に整備していません。最初の作業として、アプリケーションのすべてのエクスポート関数に TSDoc のドキュメントコメントを追加します。

また、デザインチームはゲームを見つけやすくしたいと考えています。ゲーム詳細ページに同じカテゴリーの別のゲームを表示し、ユーザーが閲覧を続けられるようにします。完成度の高い実装はまだ必要なく、UX の受け入れテストに使えるものを求めています。現在は作業を妨げる要因ですが、より優先度の高い Issue もあります。

どちらも優先順位が下がりやすいタスクの例で、Copilot cloud agent に依頼するのに適しています。開発者が別の作業を進める間、cloud agent は非同期で対応できます。後から Copilot の成果をレビューし、期待どおりか確認できます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は人間の開発者と同じように、バックグラウンドでタスクを実行できます。[GitHub Issue を Copilot に割り当てる][assign-issue]など、いくつかの方法で依頼できます。割り当てると、Copilot は進行状況を追跡する下書きプルリクエストを作成し、環境を設定して作業を始めます。進行中または完了後にセッションの内容を確認できます。提案された解決方法をレビューできる状態になると、プルリクエストで通知されます。

## 適切な範囲の指示が重要

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見単純な操作でも、詳細を確認すると複雑なことがあります。[Copilot cloud agent に依頼するタスクの範囲][cloud-agent-best-practices]を慎重に決めてください。AI ペアプログラマーとして扱い、段階的に進めながら学び、試し、適応します。生成 AI を導入しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習では、カスタム指示と Copilot への影響を学びました。カスタム指示の演習を完了していれば `.github/copilot-instructions.md` の効果を確認済みですが、そうでない場合はこの機会に確認します。cloud agent に作業を割り当てる前に、リポジトリに含まれる指示ファイルを読み取り専用で確認し、後で影響を見つけられるようにします。

リポジトリの GitHub Web UI または起動済みの Codespace で、次のファイルを開きます。

- `.github/copilot-instructions.md`: **Code standards** セクション、特に TypeScript の規約と TSDoc ドキュメントコメントの要件を確認します。
- `.github/instructions/unit-tests.instructions.md`: `applyTo` frontmatter を確認します。この指示は `**/*.test.ts` ファイルに適用されます。

次のセクションで `Code lacks documentation` Issue を cloud agent に割り当てると、生成された PR に TSDoc コメント、TypeScript の規約、コメントヘッダーが反映されているか確認できます。後の演習で PR をレビューするときにも、ここでの指示を確認します。

## Copilot cloud agent 用の開発環境を設定する

コードを作成する場合、誰が作業するかにかかわらず、適切な環境とセットアップスクリプトが必要です。ソフトウェアエンジニアと同じように作業する Copilot にタスクを割り当てる場合も同様です。

Cloud agent は GitHub Actions を使って作業環境を用意します。`.github/workflows/copilot-setup-steps.yml` に[セットアップワークフロー][setup-workflow]を作成すると、作業前に必要な開発ツールと依存関係を準備できます。このワークショップでは、Node.js、プロジェクトの依存関係、E2E テスト用のブラウザーバイナリ、単一 Astro アプリ用に移行・シード済みの SQLite データベースを使えるよう、事前に構成しています。

```yaml
name: "Copilot Setup Steps"

# リポジトリの "Actions" タブからセットアップ手順をテストできます
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # この手順に必要な最低限の権限を設定します。Copilot は自身の操作用のトークンを使用します。
    permissions:
      # 依存関係のインストールなど、セットアップ手順の一部としてリポジトリをクローンする場合は `contents: read` 権限が必要です。ここでクローンしない場合は、手順の完了後に Copilot が自動でクローンします。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ - Node.js (アプリ全体は Astro + Drizzle/libSQL です)
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

      # ビルドとテストで使用するデータが揃うよう、ローカル SQLite データベースを移行してシードします。
      - name: Set up the database
        run: npm run db:setup
```

一般的な GitHub ワークフローと似ていますが、次の点に注意します。

- `copilot-setup-steps` という 1 つのジョブがあり、Copilot が PR の作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーがあるため、リポジトリの **Actions** タブから手動でワークフローを実行できます。Copilot が使うのを待たずに、正常に実行されるかテストできます。

## ドキュメントを追加する

ドキュメントの重要性は知られていますが、古い情報が残っていたり、まったくなかったりするプロジェクトは少なくありません。こうした技術的負債は後回しになりやすく、生産性や保守性を下げ、新しい開発者が参加しにくくなります。Copilot はドキュメントの作成が得意なので、cloud agent に依頼するのに適したタスクです。バックグラウンドで必要なドキュメントを作成し、後の演習で成果をレビューします。

1. 新しいブラウザータブで github.com の自分のリポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して Issue の作成画面を開きます。
4. **Blank issue** を選択します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択し、割り当てダイアログを開きます。

    ![Issue に Copilot を割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

    ![Copilot の割り当ての詳細](../../../_images/ex4-assign-copilot-details.png)

10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のような名前です。PR が一覧に表示されない場合は、少し待ってからブラウザーを更新します。
12. 数分後、Copilot が To Do リストを作成したことを確認します。

> [!NOTE]
> Copilot の To Do リストが PR に表示されるまで数分かかることがあります。Copilot は環境を作成し (前述のワークフローを実行)、プロジェクトを分析して、問題に取り組む方法を判断しています。

13. リストを確認し、実行予定のタスクを確認します。
14. PR のタイムラインを下にスクロールし、Copilot が Issue の作業を始めたことを確認します。
15. **View session** ボタンを選択します。

    ![Copilot セッションの表示](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新された状態が表示されるまで、ブラウザーを更新する必要がある場合があります。

16. セッションをスクロールして、Copilot の作業を確認します。コードの調査、状態の把握、計画の判断、コードの作成などが行われます。

通常、完了まで数分かかります。Copilot cloud agent の主な利点は非同期でタスクを実行できることです。別の Issue も cloud agent に割り当てた後、アプリケーションの機能を追加するコードを書く作業に移ります。

## ゲーム詳細ページに関連ゲームを追加する

GitHub Copilot cloud agent の利点の 1 つは作業を分担できることです。デザインチーム向けに関連ゲームのセクションを追加するのは時間のかかる作業ではないかもしれませんが、その時間を別の仕事に使うこともできます。cloud agent に割り当てましょう。

1. github.com の自分のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して Issue 作成画面を開きます。
4. **Blank issue** を選択します。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択し、割り当てダイアログを開きます。

    ![Issue に Copilot を割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

Issue の最初のコメントに 👀 が表示され、Copilot が作業を始めたことが分かります。

![目の絵文字で Copilot が Issue に取り組んでいることを示す画面](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼にも取り組み始めました。cloud agent はソフトウェアエンジニアと同じように作業するため、常に監視する必要はありません。完了後にレビューできます。次はカスタムエージェントの作成と使用を学びます。

## まとめと次のステップ

[GitHub Copilot cloud agent][copilot-agents]について学びました。cloud agent に Issue を割り当て、非同期で作業させることができます。技術的負債への対応、新機能の追加、別のフレームワークへのコード移行などに利用できます。

この演習では、次の内容を学びました。

- コード生成用の環境をカスタマイズする。
- 操作が安全に行われるようにする。
- Issue の範囲を明確にする重要性を理解する。
- Issue を Copilot に割り当てる。

cloud agent がバックグラウンドで作業している間に、カスタムエージェントの作成と使用に進みます。[Copilot cloud agent は MCP サーバー][cloud-agent-mcp]も使用でき、前のモジュールで学んだカスタム指示も利用できます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップワークフローに関するベストプラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
