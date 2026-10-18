---
title: "レッスン 7 - QA エージェントの作成と使用"
description: "Issue の要件、quality-checks スキル、Playwright MCP を組み合わせる QA カスタムエージェントを作成します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

`quality-checks` スキルを使って自動チェックを実行し、Playwright MCP でブラウザー上のフィルター機能を確認しました。次は、明確に定義した QA プロセスを持つカスタムエージェントに、これらの機能をまとめます。

このレッスンでは、次の内容を学習します。

- カスタムエージェントが指示、スキル、MCP ツールと連携する方法を確認します。
- 再利用可能な品質保証プロファイルを作成して確認します。
- QA エージェントを選び、フィルター機能の Issue に照らして結果をレビューします。

## シナリオ

Tailspin Toys では、プルリクエスト (PR) を開く前に、要件、コード品質、自動チェック、テストカバレッジ、ブラウザーでの動作を一貫して確認したいと考えています。カスタムエージェントを使うと、QA プロセスを調整して再利用可能なレポートを作成できます。

## カスタムエージェントとは

カスタムエージェントは、Markdown プロファイルで定義する Copilot の専門的な役割です。プロファイルには、エージェントの目的、指示、利用できるツールを記述します。このワークショップでは `.github/agents/qa.agent.md` に QA の役割を定義し、Copilot CLI で選択します。

これまでに使ったカスタマイズには、それぞれ異なる役割があります。リポジトリの指示はチームの標準を説明し、`quality-checks` スキルは繰り返し実行できるチェックをまとめ、Playwright MCP はブラウザーツールを提供します。QA プロファイルは、これらを使って要件を評価し、結果を報告する方法を Copilot に指示します。これらの置き換えや、別の会話の開始は必要ありません。

## QA プロファイルを作成する

機能 PR を開く前に、再利用可能な QA プロファイルを作成するよう Copilot に依頼します。このプロファイルでは、実施するチェックだけでなく、守るべき境界も定義します。

1. Codespace に戻り、フィルター機能の会話が Interactive モードであることを確認します。
2. 既定のエージェントに、次のカスタムエージェントを作成するよう依頼します。

   ```plaintext
   Create a custom agent named QA in .github/agents/qa.agent.md. It should check features against their issues and agreed requirements, follow the repository instructions, run the quality-checks skill, use Playwright MCP to verify behavior, and add tests when coverage is missing.

   Have it report each requirement as pass, fail, or blocked with supporting evidence. It must ask before changing implementation code, and it must not commit changes or open pull requests.

   Just create the profile for now so I can review it.
   ```

## プロファイルを確認する

新しいエージェントを使う前に、意図した QA ワークフローと境界が設定されていることを確認します。未完成のエージェントや範囲が広すぎるエージェントが、検証だけを依頼した機能を変更してしまうことを防げます。

1. `/diff` を入力し、`.github/agents/qa.agent.md` を開きます。
2. frontmatter を確認します。`description` は必須で、`name` は任意ですが、指定すると分かりやすい表示名になります。
3. プロファイルの指示を読み、QA が要件を起点にし、リポジトリの指示に従い、`quality-checks` スキルを実行して Playwright MCP を使用することを確認します。
4. QA が根拠を示して結果を報告し、実装コードの変更前に確認を求め、変更をコミットせず、PR も作成しないことを確認します。
5. 必要な役割や境界が欠けている場合は、続行前に既定のエージェントへ修正を依頼します。

## Issue に照らして QA を実行する

Copilot CLI は会話の開始時にプロジェクトのエージェントを読み込みます。プロファイルを作成した後、同じフィルター機能の会話を再開して QA を選択すると、コンテキストにある Issue と計画の決定を利用できます。

1. 次のプロンプトを使ってエージェントを有効にします。

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> エージェントを作成した直後は、使用可能なエージェントの一覧に表示されない場合があります。次のコマンドで有効にできます。

2. フィルター機能をレビューするよう QA に依頼します。

   ```plaintext
   Review the filtering feature against the issue and the decisions in our plan. Is it ready for a PR?
   ```

3. QA エージェントが作業する様子を確認します。
4. 作業が終わったら、レポートを読みます。

## まとめと次のステップ

再利用可能な専門家の役割を追加し、その成果をレビューしました。このレッスンでは、次の内容を学びました。

- カスタムエージェントと指示、スキル、MCP ツールとの連携方法を確認しました。
- 再利用可能な品質保証プロファイルを作成して確認しました。
- QA エージェントを選び、フィルター機能の Issue に照らして結果をレビューしました。

実装、スキル更新、QA プロファイル、テスト、検証結果の準備ができました。次は、[これらを機能 PR にまとめ、Agent Merge を使います][next-lesson]。

## リソース

- [Copilot CLI 用のカスタムエージェントを作成する][create-agents]
- [カスタムエージェントの構成][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
