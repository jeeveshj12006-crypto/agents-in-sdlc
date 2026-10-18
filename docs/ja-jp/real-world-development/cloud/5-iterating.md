---
title: "演習 5: GitHub Copilot の作業を反復して改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

このラボでは、ユーザー体験の改善と機能追加を目的とするいくつかのタスクについて GitHub Copilot と作業してきました。コードへのドキュメント追加、デザインチームが改善を検討するための関連ゲーム機能、ハイコントラストとライトモードを含むアクセシビリティ機能を依頼しました。変更を確認し、必要に応じて Copilot にフィードバックを返し、作業を改善します。

### シナリオ

生成 AI を導入しても、ソフトウェア設計と DevOps の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスを進めます。チームのレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能に対する GitHub Copilot の提案を確認します。

## GitHub Copilot cloud agent とセキュリティ

Copilot cloud agent は非同期かつ監視なしでタスクを実行するため、安全性を保つためのセキュリティ制約があります。

- Copilot はリポジトリへの読み取りアクセスを持ち、書き込みできるのは作業に使用するブランチだけです。
- Cloud agent は GitHub Actions 内で、作業用の一時的な独立環境を作成します。
- GitHub Actions のワークフローは、人間の承認がなければ実行されません。
- [外部リソースへのアクセス][agent-firewall]は、MCP サーバーを含め、既定では制限されています。

## 生成されたドキュメントをレビューする

GitHub Copilot cloud agent が最初に作成した、コードへのドキュメント追加 PR を確認します。github.com の標準的な PR インターフェイスを使います。

> [!NOTE]
> PR に GitHub Copilot がファイアウォールでブロックされたという警告が表示される場合があります。Copilot は、外部 MCP サーバーへの呼び出しを含む外部リソースへのアクセスが既定では制限されているため、これは想定された動作です。必要に応じて [Copilot cloud agent のファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com の自分のリポジトリに戻ります。
2. **Pull Requests** を選択して PR の一覧を開きます。
3. **Add missing documentation** などのタイトルが付いた PR を開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、PR のタイトルに **[WIP]** が含まれます。その場合は完了するまで待ちます。数分かかることがあるため、休憩したり、ここまでに学んだ内容を振り返ったりしてください。

4. PR の準備ができたら **Files changed** タブを選択し、変更をレビューします。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. 新しく追加された TSDoc ドキュメントコメントなど、更新されたコードを確認します。変更内容は状況によって異なります。TSDoc コメント、TypeScript の規約、コメントヘッダーが含まれているか確認します。これらは演習 2 で確認したカスタム指示ファイルによるものです。
6. 問題がなければ **Conversation** タブに戻り、下へスクロールします。
7. ワークフローの承認待ちを示す表示があることを確認します。
8. 承認待ちのワークフローがある場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. PR のチェックにワークフローが追加されます。すべて正常であれば、単一 Astro アプリのプロジェクトチェックが成功します。完了まで数分かかる場合があります。

## GitHub Copilot に変更を依頼する

PR で Copilot と作業する方法は一方通行ではありません。チームメンバーと同じように、PR やコードのインラインコメントで Copilot にメンションできます。Copilot はコメントを確認し、対応するセッションを開始します。結果は確率的なので、特定の文面を指定することはできません。たとえば、次のような変更を依頼できます。

- 各コードファイルの先頭に、ファイルの役割を説明するコメントヘッダーを追加する。
- TypeScript ファイルと Astro ファイルに TSDoc ドキュメントコメントを追加する。
- Astro アプリの構成を説明する README を作成する。

1. 上記の例などを使い、生成されたドキュメントの変更を依頼するコメントに **@copilot** を付けます。
2. **View Session** を選択して Copilot の作業を確認します。Copilot は更新用の新しいセッションを開始します。
3. **Back to pull request** を選択すると PR に戻れます。

    ![プルリクエストに戻る](../../../_images/ex7-back-to-pr.png)

4. Copilot が変更を完了すると、PR に新しいコミットが表示されます。
5. **Files changed** タブで変更をレビューします。

納得できるまで反復して改善します。完了したら PR をドラフトからレビュー可能な状態に変更し、`main` にマージできます。

![PR をレビュー可能な状態にする](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

次に、ゲーム詳細ページに関連ゲームを表示する Issue に対して Copilot が作成した PR を確認します。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** などのタイトルの PR を選択します。
4. **Files changed** タブで、生成されたコードをレビューします。
5. 問題がなければ **Conversation** タブに戻り、下へスクロールします。
6. ワークフローの承認待ちを示す表示があることを確認します。
7. 承認待ちのワークフローがある場合は **Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

8. PR のチェックにワークフローが追加されます。すべて正常であれば、単一 Astro アプリのプロジェクトチェックが成功します。完了まで数分かかる場合があります。
9. **省略可能:** Codespace でこのブランチに切り替え、関連ゲーム機能を手動テストできます。Codespace のターミナルで、Copilot が作成したブランチ名 (例: **copilot/fix-8**) に置き換えて次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot は関連ゲーム機能を作成しました。ほかの作業と同様に、cloud agent に更新を依頼して反復できます。たとえば、表示する関連ゲームの数を調整したり、コメントヘッダーと TSDoc コメントを追加したりできます (この Issue を割り当てた時点では、カスタム指示を更新する前でした)。**Conversation** タブでコメントすると、Copilot が確認して新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、カスタムアクセシビリティエージェントが実装した機能を確認します。この PR には演習 3 で割り当てたハイコントラストモードと、演習 4 で Mission Control から追加を依頼したライトモードの両方が含まれます。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** などのタイトルの PR を選択します。

> [!NOTE]
> Copilot がまだ作業中の場合、PR のタイトルに **[WIP]** が含まれます。完了するまで待ちます。数分かかる場合があります。

4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 実装を確認し、特に次の点に注意します。
   - モードを切り替える UI コンポーネント
   - ユーザー設定を保存する Local Storage の利用
   - ハイコントラストモードとライトモードの CSS またはスタイル
   - アクセシビリティ属性 (ARIA ラベル、キーボード操作など)
   - モード切り替えを管理する JavaScript/TypeScript コード

6. 問題がなければ **Conversation** タブに戻り、下へスクロールします。
7. ワークフローの承認待ちを示す表示があることを確認します。
8. 承認待ちのワークフローがある場合は **Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. PR のチェックにワークフローが追加されます。すべて正常であれば、単一 Astro アプリのプロジェクトチェックが成功します。完了まで数分かかる場合があります。
10. **省略可能:** Codespace でこのブランチに切り替え、アクセシビリティ機能を手動でテストできます。Codespace のターミナルで、Copilot が作成したブランチ名に置き換えて次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    次にアプリケーションを起動し、ブラウザーでハイコントラストモードとライトモードを試します。ページの再読み込み後も設定が保持されることを確認してください。

カスタムアクセシビリティエージェントが、アクセシビリティのベストプラクティスに沿うよう Copilot をガイドしたことを確認します。改善すべき点があれば、前の PR と同じようにコメントで **@copilot** に更新を依頼できます。

## 省略可能な演習 — さらに委任する

実際のバックログ項目を cloud agent に任せ、その間に別の作業を進めることで、効果的に利用できます。習慣を身につけるため、リポジトリに Issue を作成して Copilot に割り当ててみます。

- ゲーム詳細ページに支援者向けの関心フォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセスヘルパーに入力検証とエラー処理を追加する。

## まとめ

Cloud agent のハーネスを完了しました。これらのレッスンでは、次の作業を行いました。

- リポジトリに用意されたカスタム指示ファイルを確認し、後で cloud agent の出力への影響を確認できるようにしました。
- Issue を Copilot cloud agent に割り当て、環境の準備、計画、非同期の実行を確認しました。
- アクセシビリティ用のカスタムエージェントを作成して使用し、ハイコントラストモードとライトモードを追加しました。
- Copilot agents ページを Mission Control として使い、実行中のアクセシビリティセッションを監視・調整しました。
- cloud agent の PR をレビューして反復し、**@copilot** に変更を依頼し、ワークフローを承認しました。

## レビューと次のステップ

Cloud agent のハーネスを完了しました。さらに学びたい場合は、ここで練習した内容を補う次のハーネスを確認してください。

- 🖥️ **[VS Code ハーネス](../../vscode/)** — IDE から Copilot Chat のエージェントモードと MCP 統合を体験します。
- 💻 **[CLI ハーネス](../../cli/)** — Plan と Autopilot、エージェントスキル、カスタム QA エージェント、Playwright MCP、Agent Merge、省略可能な `/delegate` による cloud agent への連携を使い、ターミナルからレビュー済みの 2 つの変更をリリースします。

自分のリポジトリでは、次の方法も試せます。

- リファクタリングやテストカバレッジの Issue を cloud agent に割り当てる。
- 別分野のカスタムエージェントを作成する。
- 別の技術スタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot エージェントについて][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップワークフローに関するベストプラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールを構成する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
