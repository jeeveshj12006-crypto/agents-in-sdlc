---
title: "レッスン 9 - GitHub Copilot CLI のスラッシュコマンド"
description: "コンテキスト管理、モデルの選択、セッションの共有、省略可能なクラウドへの委任に使うスラッシュコマンドを確認します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

多くの CLI ツールと同じように、GitHub Copilot CLI には操作用のスラッシュコマンドが多数あります。高度な機能や内部情報、追加の構成オプションを利用できます。これまで `/diff`、`/mcp`、`/skills`、`/agent`、`/pr` などを使いました。ここでは、さらに便利なコマンドを確認します。

このレッスンでは、次の内容を学習します。

- `/context` と `/compact` で、Copilot が会話のコンテキストを管理する方法を確認します。
- `/model` で利用できるモデルを確認します。
- `/share` でセッションをエクスポートまたは共有する方法を学びます。
- 並行作業、worktree、クラウドエージェントへの委任に使う任意のコマンドを確認します。

## シナリオ

CLI の主要なワークフローを完了しました。ここでは、コンテキスト管理、モデルの切り替え、セッションの共有、省略可能な作業の [Copilot cloud agent への委任][about-cloud-agent]などの機能を確認します。

## Copilot CLI のコンテキストを確認する

大規模または複雑なタスクでは、モデルのコンテキストウィンドウの上限に達する場合があります。Copilot CLI は必要に応じて会話を自動的に圧縮しますが、スラッシュコマンドを使って自分で確認したり圧縮したりできます。

1. Codespace に戻り、Copilot CLI が起動していない場合はリポジトリのルートから起動します。
2. 次を入力します。

   ```plaintext
   /context
   ```

3. モデル、現在のトークン使用量、システム指示、ツール、メッセージ、空き領域へのコンテキストの割り当てを確認します。
4. 会話を圧縮します。

   ```plaintext
   /compact
   ```

5. もう一度 `/context` を入力して結果を比べます。会話が短い場合は、あまり変化しないことがあります。

> [!NOTE]
> コンテキストウィンドウがいっぱいになると、Copilot CLI は自動で圧縮します。タイミングを自分で選ぶ場合は `/compact` を使います。関係のないタスクに切り替えて新しい会話を始める場合は、`/clear` または `/new` を使います。

## モデルを選択する

モデルによって得意なことが異なり、開発者によって好みも異なります。Copilot CLI では、使用可能なモデルの表示と選択ができます。

1. 次を入力します。

   ```plaintext
   /model
   ```

2. 使用できるモデルと使用状況を確認します。
3. 現在のモデルを維持するか、別のモデルを選ぶか、<kbd>Esc</kbd> を押して一覧を閉じます。

## セッションを共有する

チームで学びを共有すると、AI ツールの活用方法を改善できます。`/share` コマンドでは、セッションを Markdown または HTML ファイルにエクスポートしたり、共有リンクを作成したり、GitHub gist として公開したりできます。

1. `/help` を入力し、インストール済みバージョンの `/share` オプションを確認します。
2. 共有する場合は、ローカルに Markdown を出力する `/share file` など、用途に合った方法を選びます。
3. エクスポートした内容を他の人に送ったり公開したりする前にレビューします。セッションのエクスポートには、プロンプト、応答、プロジェクトの詳細が含まれる場合があります。

リンクや gist の公開は任意です。チームが公開する意図のないリポジトリや会話の内容は公開しないでください。

## 省略可能: 作業を拡大する、または委任する

ワークショップの主要な作業は完了しました。Copilot CLI には、より大きなタスクに使えるコマンドもあります。

- `/fleet` は独立したサブタスクをサブエージェントに割り当て、並行して実行できます。
- `/worktree` は別のタスク用に隔離された Git worktree を作成できます。
- `/delegate` は Copilot cloud agent にタスクを送信できます。エージェントは非同期で作業し、プルリクエストを開く場合があります。

これらのコマンドは、worktree やリモートでの作業が追加される場合があるため任意です。試すときは、新しく範囲を明確にしたタスクを始め、通常のワークフローで結果をレビューしてください。非同期エージェントの作業を詳しく学ぶには、[cloud agent ワークショップ][cloud-workshop]に進みます。

## まとめと次のステップ

スラッシュコマンドを使うと、Copilot CLI を構成し、セッションを共有し、内部の動作を確認できます。このレッスンでは、次の内容を学びました。

- `/context` と `/compact` でコンテキスト管理を確認しました。
- `/model` で使用できるモデルを確認しました。
- `/share` でセッションをエクスポートまたは共有する方法を学びました。
- 並行作業、worktree、クラウドへの委任に使うコマンドを確認しました。

Copilot CLI にはほかにも多くのコマンドや機能があります。[ワークショップを振り返り][next-lesson]、次の学習内容を確認しましょう。

## リソース

- [Copilot CLI コマンドリファレンス][cli-reference]
- [Copilot CLI のコンテキスト管理][context-management]
- [Copilot cloud agent について][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
