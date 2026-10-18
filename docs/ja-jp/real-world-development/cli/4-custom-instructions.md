---
title: "レッスン 4 - カスタム指示で Copilot をガイドする"
description: "リポジトリの指示を確認し、ドキュメント標準を追加して、フィルター機能のコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

生成 AI を使うときはコンテキストが重要です。特定の方法でタスクを進めたい場合は、その指針を Copilot が参照できるようにします。[指示ファイル][instruction-files]では、作成するコードだけでなく、その構成方法も伝えられます。フィルター機能を構築したので、Copilot が使った指示を確認し、ドキュメント標準を追加してコードに適用します。

このレッスンでは、次の内容を学習します。

- リポジトリの指示とパス別の指示ファイルがエージェントに届くしくみを確認します。
- コーディング標準に従うため、指示ファイルを更新します。
- 指示ファイルがコードに与える影響を確認します。

## シナリオ

Tailspin Toys の開発チームには、開発方法に関するガイドラインがあります。

- コメントではコードを言い換えるのではなく、意図や自明でない判断を説明します。
- `db/` と `src/lib/` のエクスポート関数には、目的、パラメーター、戻り値を TSDoc/JSDoc で記載します。`db` を注入可能な引数として受け取る場合は、その説明も含めます。
- 再利用可能な Astro コンポーネントでは `Props` の契約を説明し、関連コードが変わった場合はコメントを最新の状態に保ちます。
- 既存のフォーマットや lint の指針を維持します。

指示ファイルを使うと、Copilot がこれらの開発慣行に沿って作業するための情報を提供できます。

## 指示ファイル

カスタム指示を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードを得られます。コメントの方針、ライブラリ、コーディング規約などを指定できます。リポジトリ全体に適用する指示と、特定のファイルやタスクに適用する指示を作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md`: リポジトリへのすべての依頼で Copilot に送信される、単一の指示ファイルです。多くの依頼に関係するプロジェクト情報を記載します。
- `.github/instructions/*.instructions.md`: 特定の言語、ファイル種別、タスクに対するガイドラインを記載します。

> [!NOTE]
> ほかの指示形式とそのサポート状況は環境によって異なります。特定の形式を前提とする前に、[カスタム指示のサポート状況][custom-instructions-support]を確認してください。

## プロジェクトのカスタム指示を確認する

スタータープロジェクトには指示ファイルが含まれています。変更する前に、既存の指示とその効果を確認しましょう。

1. Codespace に戻ります。
2. Codespaces エディターで (ターミナルではなく) `.github/copilot-instructions.md` を開きます。
3. プロジェクトの概要とコーディング指針を確認します。この指示は、このリポジトリで Copilot とやり取りするすべての場面に適用されます。
4. `.github/instructions` フォルダーを開き、ファイルを確認します。Astro、Drizzle のデータレイヤー、テストなどの指示があります。
5. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。この glob で指示の適用対象ファイルを指定しています。
6. `.github/instructions/drizzle.instructions.md` を開き、ほかの指示ファイルや既存プロジェクトファイルへのリンクを確認します。大きな指示セットを再利用可能な小さなファイルに分け、参考例を示せます。

## チームのガイドラインに合わせて指示を更新する

既存の指示は有用ですが、まだ不足があります。コアの `copilot-instructions.md` を変更し、新しく生成する TypeScript に TSDoc コメントを追加するよう求めます。

1. `.github/copilot-instructions.md` の **Code formatting guidance** セクション (35 行目付近) を探します。
2. 最後の箇条書きとして、次を追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

ファイルは自動的に保存されます。

## 更新した指針を適用する

Copilot CLI は会話の開始時にリポジトリの指示を読み込みます。指示を保存したら、フィルター機能の会話を終了して再開します。これにより、機能のコンテキストを保ったまま新しい指示を読み込めます。

1. フィルター機能の会話を開いている場合は、`/exit` を入力して閉じます。Copilot CLI は会話を自動保存します。
2. リポジトリのルートで `game-filters-cli` ブランチをチェックアウトした状態から、保存済みの会話を再開します。

   ```bash
   copilot --resume
   ```

   会話の選択を求められた場合は、フィルター機能の会話を選び、`game-filters-cli` ブランチがチェックアウトされていることを確認します。

3. 更新した指針を適用するよう Copilot に依頼します。

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. `/diff` を入力し、変更された TypeScript ファイルを確認します。追加された TSDoc コメントがコードを正確に説明していることを確認します。

## まとめと次のステップ

Copilot CLI が指示をコンテキストとして使うしくみを確認し、新しい標準を機能に適用しました。このレッスンでは、次の作業を行いました。

- リポジトリの指示とパス別の指示ファイルがエージェントに届くしくみを確認しました。
- コーディング標準に従うため、指示ファイルを更新しました。
- 指示ファイルがコードに与える影響を確認しました。

次は、[再利用可能な quality-checks スキルをカスタマイズして実行し][next-lesson]、lint とテストを一貫して実行します。

## リソース

- [Copilot CLI にカスタム指示を追加する][instruction-files]
- [カスタム指示のサポート状況][custom-instructions-support]
- [効果的なカスタム指示を作成するためのベストプラクティス][instructions-best-practices]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
