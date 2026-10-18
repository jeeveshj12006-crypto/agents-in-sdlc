---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前の 2 つの演習では、ユーザー体験の改善と機能追加を目的とする 3 つのタスクを Copilot cloud agent に依頼しました。cloud agent は非同期かつ自律的に動作しますが、タスクを監視することも重要です。

GitHub.com の[agents ページ][agents-page]など、cloud agent に割り当てたタスクを管理するためのツールがあります。この Mission Control では、オープンなプルリクエスト (PR) があるエージェントタスクをすべて確認できます。実行された操作を調べ、進行中のセッションに指示を追加することもできます。

このレッスンでは、次の内容を学習します。

- agents ページで cloud agent のタスクを監視します。
- 実行中のセッションを調整し、機能を追加します。

## シナリオ

チームはハイコントラストモードの作成を依頼した後、ライトモードも追加することにしました。サイトのスタイルと切り替え機能を更新する作業の一部にするのが適切です。ライトモードとハイコントラストモードの両方を実装するよう、エージェントの作業を調整します。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたすべてのタスクの現在の状態を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)を開きます。
2. メインペインと左ペインに表示されたタスクを確認します。次のような、Copilot に割り当てたタスクが表示されます。
   - コードベースのドキュメント更新
   - ゲームを変更する API の生成
   - Web サイトのハイコントラストモード追加
3. 実行中のタスクを選択し、Copilot が行った作業を確認します。次のような手順が含まれる場合があります。
   - リポジトリからのコードのチェックアウト
   - Copilot 用環境の作成
   - MCP サーバーのセットアップ
   - タスクを完了するための各種作業

> [!NOTE]
> 表示される手順は、Copilot の作業状況や選択したアプローチによって異なります。

4. 右側に表示されるプルリクエスト (PR) ペインも確認します。PR と変更ファイルを表示して、追加の監視を行えます。

## Cloud agent の作業を調整する

実行中のタスクを確認したので、エージェントがハイコントラストモードを作業している間にライトモードも追加するよう依頼します。

1. ハイコントラストモードのセッションを選択します。タイトルは Copilot が付けた名前と作業状況によって異なります。

    ![Mission Control のアクセシビリティセッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了して作業が始まるまで、数分間セッションを確認します。作業開始後は、同様のメッセージが表示されます。
3. **Steer active session while Copilot is working** ダイアログに、次のプロンプトを入力します。

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![agents ページの cloud agent タスクにある「Steer active session while Copilot is working」ダイアログ](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業に含めたことを確認します。

## Copilot の作業を待つ

前と同じように、Copilot は更新されたタスクに取り組みます。メッセージを送った時点で実行中の手順が完了した後、新しい依頼を作業に反映します。

この処理にも数分かかります。ここまで学んだ内容を振り返る時間にしましょう。

## まとめと次のステップ

Copilot cloud agent のタスクを監視し、ガイドする中心的な場所である Copilot agents ページを確認しました。Mission Control では、進行中または完了したタスクを追跡し、作業内容をレビューし、実行中のタスクの範囲や指示を変更できます。

このレッスンでは、次の内容を学びました。

- Mission Control と agents ページで cloud agent のタスクを監視しました。
- 実行中のセッションに追加機能を依頼しました。

Copilot がアクセシビリティ機能に取り組んでいる間に、[Copilot が作成したプルリクエストを反復して改善します][next-lesson]。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
