---
title: "演習 4 - カスタムエージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## カスタムエージェントとは

GitHub Copilot の[カスタムエージェント][custom-agents-concept]を使うと、開発ワークフローの特定のタスクや分野に特化した AI アシスタントを作成できます。リポジトリの `.github/agents` にある Markdown ファイルで、Copilot に重点を絞った指示、ベストプラクティス、コーディングパターン、分野固有の知識を与えます。チームの専門知識を、[WCAG][wcag] 準拠を確認するアクセシビリティエージェント、安全なコーディングを確認するセキュリティエージェント、一貫したテストパターンを維持するテストエージェントなどにまとめられます。

カスタムエージェントは、プロジェクトの `.github/agents` またはグローバルな `~/.copilot/agents` の Markdown ファイルで定義します。各ファイルには `name` と `description` を含む YAML frontmatter と、エージェントの動作、専門知識、指示を定義する Markdown プロンプトを記載します。

### カスタムエージェントとエージェントスキルの違い

カスタムエージェントと[エージェントスキル][agent-skills-concept]には共通点があります。どちらも主に Markdown ファイルで定義し、AI に作業方法を伝えます。簡単に分けると、**カスタムエージェントは作業者、スキルはツール**です。

カスタムエージェントには独自のコンテキストウィンドウがあり、作業の一環としてスキルや別のエージェントを調整できます。この演習のアクセシビリティエージェントは、アクセシビリティガイドラインに照らしてサイトをレビュー・更新します。必要に応じて PR ワークフロースキルやテスト用スキルを呼び出せます。

> [!NOTE]
> カスタムエージェントの作成方法に唯一の正解はありません。AI のほかの使い方と同じように、環境やシナリオに合わせて試し、改善してください。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

この演習では、次の内容を学習します。

- カスタムエージェントの定義方法を確認します。
- Copilot Chat でアクセシビリティエージェントを使ってハイコントラストモードを実装します。

## シナリオ

Tailspin Toys は、視覚的な能力や好みにかかわらず誰もがクラウドファンディングプラットフォームを利用できるように取り組んでいます。現在のダークテーマはテキストと背景色のコントラストが十分でなく、読みにくいという意見がユーザーから寄せられました。デザインチームは、ユーザーが切り替えられるハイコントラストモードを求めています。

アクセシビリティは重要なため、カスタムエージェントを使って機能をすばやく作成します。

## アクセシビリティ用カスタムエージェントを確認する

アクセシビリティ用のカスタムエージェントはすでに作成されています。Codespace に戻り、内容を確認します。

Codespace に戻り、ターミナルを開いて、演習 3 のフィルター機能 PR とアクセシビリティ作業を分けるため、`main` から新しいブランチを作成します。

```bash
git checkout main
git pull
git checkout -b accessibility-vscode
```

1. `.github/agents/accessibility.md` を開きます。
2. `name` と `description` を含む YAML frontmatter を確認します。

> [!CAUTION]
> カスタムエージェントには `name` と `description` の frontmatter が必要です。

3. 次のセクションを読みます。
   - アクセシブルな Web サイトのコード生成に関する主な責任
   - アクセシビリティのベストプラクティス
   - HTML、CSS、JavaScript のコード例
   - よくある落とし穴や間違い

## Copilot Chat でカスタムエージェントを使う

VS Code の Copilot Chat ビュー下部にあるエージェント一覧には、`.github/agents` に定義されたカスタムエージェントが表示されます。エージェントを選ぶと、その指示とツールを使って会話できます。

> [!TIP]
> **Copilot Chat を開く**
>
> 次の手順を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択します。モードとモデルの選択は演習ごとに異なるため、必要な手順で説明します。

1. エージェント一覧で **Agent** を選択します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

2. Chat ビュー下部にあるエージェント一覧を開きます (既定では **default** が選択されています)。
3. 利用可能なエージェントから **Accessibility agent** を選択します。
4. 次のプロンプトを送信します。

    ```plaintext
    Add a high-contrast mode to the site. There should be a toggle for high contrast which the user can set, and the setting should persist across page reloads using local storage on the browser.
    ```

5. Copilot Chat がコードベースを調査し、変更を提案してファイルを編集します。編集はファイルパスと差分とともにチャットに表示されます。
6. エージェントが変更した作業対象を示す **Files changed** の表示が更新されます。

> [!NOTE]
> 処理には数分かかることがあります。Copilot は Astro コンポーネント、CSS、関連テストなどを実際に変更します。

次の演習で、この作業の進行状況を確認し、指示を追加します。

## まとめと次のステップ

GitHub Copilot の[カスタムエージェント][custom-agents]について学びました。チームの専門知識と標準を再利用可能なエージェントにまとめ、Copilot の作業をガイドできます。

この演習では、次の内容を学びました。

- カスタムエージェントの定義方法を確認しました。
- Copilot Chat のエージェントモードでカスタムエージェントを使いました。

次は、エージェントの作業を[監視して調整します][next-lesson]。変更を確認し、同じセッションにライトモードの切り替えを追加します。

## リソース

- [カスタムエージェントについて][custom-agents]
- [IDE でカスタムエージェントを作成する][creating-custom-agents-ide]
- [VS Code のカスタムエージェント][custom-agents-vscode]
- [カスタムエージェントの構成][custom-agents-config]
- [awesome-copilot のカスタムエージェント][awesome-copilot-agents]

---

| [← 前のレッスン: Playwright MCP サーバーで機能をテストする][previous-lesson] | [次のレッスン: エージェントの監視と管理 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-mcp/
[next-lesson]: ../5-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[creating-custom-agents-ide]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide
[custom-agents-vscode]: https://code.visualstudio.com/docs/copilot/customization/custom-agents
[custom-agents-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
[awesome-copilot-agents]: https://github.com/github/awesome-copilot/tree/main/agents
