---
title: "演習 3 - カスタムエージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## カスタムエージェントとは

GitHub Copilot の[カスタムエージェント][custom-agents-concept]を使うと、開発ワークフローの特定のタスクや分野に特化した AI アシスタントを作成できます。リポジトリの `.github/agents` に Markdown ファイルを作成して、重点を絞った指示、ベストプラクティス、コーディングパターン、分野固有の知識を与えます。チームの専門知識を、[WCAG][wcag] 準拠を徹底するアクセシビリティエージェント、安全なコーディングを確認するセキュリティエージェント、一貫したテストパターンを維持するテストエージェントなどとして再利用できます。

カスタムエージェントは、プロジェクトの `.github/agents` またはグローバルな `~/.copilot/agents` にある Markdown ファイルで定義します。各ファイルには `name` と `description` を含む YAML frontmatter と、その後にエージェントの動作、専門知識、指示を定義する Markdown プロンプトを記載します。

### カスタムエージェントとエージェントスキルの違い

カスタムエージェントと[エージェントスキル][agent-skills-concept]には共通点があります。どちらも主に Markdown ファイルで定義し、AI に作業方法を伝えます。簡単に分けると、**カスタムエージェントは作業者、スキルはツール**です。

カスタムエージェントには独自のコンテキストウィンドウがあり、作業の一環としてスキルや別のエージェントを調整できます。このラボのアクセシビリティエージェントは、アクセシビリティガイドラインに照らしてサイトをレビュー・更新します。その際、プルリクエストのワークフロースキルやテストの実行・管理スキルを呼び出すこともできます。

> [!NOTE]
> カスタムエージェントの作成方法に唯一の正解はありません。AI のほかの使い方と同様に、環境やシナリオに合わせて試し、改善してください。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

この演習では、次の内容を学習します。

- カスタムエージェントの定義方法を確認します。
- カスタムエージェントにタスクを割り当てます。

## シナリオ

Tailspin Toys は、見え方や好みにかかわらず誰もがクラウドファンディングプラットフォームを利用できるように取り組んでいます。ユーザーから、現在のダークテーマはテキストと背景色のコントラストが十分でなく、読みにくいという意見が寄せられました。デザインチームは、ユーザーが切り替えられるハイコントラストモードを求めています。

アクセシビリティは重要なため、カスタムエージェントを使って機能をすばやく実装します。

## アクセシビリティ用カスタムエージェントを確認する

アクセシビリティ用のカスタムエージェントがすでに作成されています。Copilot の作業をガイドする内容を確認します。

Codespace に戻り、アクセシビリティ用のカスタムエージェントファイルを確認します。

1. `.github/agents/accessibility.md` を開きます。
2. `name` と `description` を含む YAML frontmatter を確認します。

> [!CAUTION]
> カスタムエージェントには `name` と `description` の frontmatter が必要です。

3. 続くセクションを読み、次の内容を確認します。
   - アクセシブルな Web サイトのコード生成に関する主な責任
   - アクセシビリティのベストプラクティス
   - HTML、CSS、JavaScript のコード例
   - よくある落とし穴や間違い

## Issue を作成して割り当てる

Mission Control は環境内のエージェントを操作する中心的な場所です。Copilot cloud agent にタスクを割り当て、監視し、方向を変えたり追加の指針を与えたりできます。まず、ハイコントラストモードの作成を Copilot に依頼します。

1. リポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して新しい Issue のダイアログを開きます。
4. **Blank issue** を選択します。
5. **Title** に `Add high contrast mode to website` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    We need a high contrast mode for the site. There should be a toggle for high contrast which the user can set. It should store the setting in local storage on the browser.
    ```

7. **Create** を選択します。
8. 右側の **Assign to Copilot** を選択して割り当てダイアログを開きます。
9. 一覧から **Accessibility agent** を選択します。

    ![カスタムエージェントと Accessibility agent が選択された Copilot cloud agent の割り当て画面](../../../_images/ex5-select-custom-agent.png)

10. **Assign** を選択します。
11. Copilot がバックグラウンドで作業を開始します。

## まとめと次のステップ

特定のタスクや分野に特化した AI アシスタントである GitHub Copilot の[カスタムエージェント][custom-agents]について学びました。チームの専門知識と標準を再利用可能なエージェントにまとめ、Copilot の作業をガイドできます。

この演習では、次の内容を学びました。

- カスタムエージェントの定義方法を確認しました。
- カスタムエージェントにタスクを割り当てました。

Copilot がハイコントラストモードを実装している間に、次は [Mission Control からエージェントのセッションを監視して調整します][next-lesson]。

## リソース

- [カスタムエージェントについて][custom-agents]
- [Organization でカスタムエージェントを使う準備][org-custom-agents]
- [Enterprise でカスタムエージェントを使う準備][enterprise-custom-agents]

---

| [← 前のレッスン: GitHub Copilot cloud agent][previous-lesson] | [次のレッスン: エージェントの監視と管理 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../2-cloud-agent/
[next-lesson]: ../4-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[org-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents
[enterprise-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents
