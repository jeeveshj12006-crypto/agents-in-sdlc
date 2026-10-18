---
title: "演習 1 - カスタム指示 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: エージェントモード →][next-lesson]

生成 AI を使うときはコンテキストが重要です。タスクの進め方や Copilot が知っておくべき背景情報を伝えるには、[指示ファイル][instruction-files]を使います。

この演習では、次の内容を学習します。

- リポジトリのカスタム指示とパス別の指示ファイルを通じて、プロジェクトのコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる方法を確認します。
- 現在の指示のまま、フィルター機能の最初のデータスライス (発行元ヘルパー) を生成します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加します。
- 同じプロンプトを再実行し、生成されたコードに新しい標準が反映される様子を確認します。
- 次の演習で更新済み指示を使えるよう、指示の変更とフィルター機能を `main` にコミットします。

> [!CAUTION]
> 生成されたコードが、設定した標準と異なる場合があります。Copilot の出力は確率的です。この演習の目的は、指示を更新した後の傾向の変化を確認することであり、出力を文字単位で一致させることではありません。

## 指示ファイル

### シナリオ

Tailspin Toys には、次のような開発慣行のガイドラインがあります。

- データレイヤーには必ず単体テストを用意します。
- UI はダークモードで、現代的なデザインにします。
- コードには TSDoc のドキュメントコメントを追加します。
- 各ファイルの先頭に、ファイルの役割を説明するコメントブロックを追加します。

指示ファイルを使い、Copilot がこれらの慣行に沿って作業できる情報を提供します。

### カスタム指示

カスタム指示を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードを得られます。コメントの方針やライブラリなどを指定できます。リポジトリ全体に適用する指示と、特定のファイルやタスクに適用する指示を作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md`: リポジトリへの**すべての**依頼で Copilot に送信される単一の指示ファイルです。技術スタック、開発内容の概要、ベストプラクティスなど、ほとんどの依頼に関係する情報を記載します。
- `.github/instructions/*.instructions.md`: 特定のタスクやファイル種別に適用する指示ファイルです。TypeScript や Astro などの言語、UI コンポーネントや単体テストなどのタスクに使えます。

> [!NOTE]
> IDE では、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や次の編集候補には適用されません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成の際にリポジトリレベルの指示と、`applyTo` frontmatter が設定された `*.instructions.md` ファイルの両方を使います。
>
> Copilot は AGENTS.md や CLAUDE.md など、ほかの規格の指示ファイルも[サポートしています][custom-instructions-support]。

### 指示ファイルを管理するベストプラクティス

- `copilot-instructions.md` には、構築するもの、プロジェクト構成、全体的なコーディング標準などのプロジェクトレベルの指針を記載します。
- `*.instructions.md` には、単体テスト、Astro コンポーネント、データレイヤーなどのファイル種別や特定のタスク向けの指示を記載します。
- 自然な言葉で明確に伝え、良いコード例と避ける例を示します。

指示ファイルの作成方法は 1 つではありません。試しながら、プロジェクトに合う方法を見つけます。

> [!TIP]
> GitHub Copilot を使うプロジェクトには、充実した指示ファイルを用意することをお勧めします。このプロジェクトには [UI 更新][ui-instructions]や [Astro][astro-instructions]などの指示があります。
>
> Copilot は指示ファイルの生成も支援できます。VS Code の **Configure Chat → Generate Agent Instructions** や Copilot CLI の `/init` など、手順は利用する環境によって異なります。
>
> テンプレートや出発点として、指示ファイル、カスタムエージェントなどが集まった [awesome-copilot][awesome-copilot] を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示を確認する

このリポジトリにはコアの `copilot-instructions.md` と、タスク別の `*.instructions.md` が含まれています。エディターまたは GitHub の Web UI で開きます。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクト概要と **Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを読みます。**Code standards** にある **GitHub Actions Workflows** の指針を含め、Copilot とのあらゆるやり取りに適用されます。
3. `.github/instructions` フォルダーを確認します。Astro、Drizzle データレイヤー、テストなどの指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` を確認します。リポジトリのルートからの相対 glob で適用対象を指定しています。たとえば `**/*.test.ts` に一致する TypeScript テストファイルが対象です。
5. このプロジェクトで単体テストを作成するときの指示を確認します。
6. `.github/instructions/drizzle.instructions.md` の末尾で、`unit-tests.instructions.md` などの指示ファイルと既存ファイルへのリンクを確認します。指示を再利用可能な小さなファイルに分け、Copilot に例を示せます (パスはリポジトリのルートではなく、指示ファイルからの相対パスです)。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** にはプロジェクトの標準が記載されていますが、コード内ドキュメントはまだ必須ではありません。次の手順で TSDoc コメントとファイルコメントヘッダーのルールを追加します。

## 変更用のブランチを作成する

変更を行う前に、ブランチを作成します。

1. 前の演習で使った Codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して新しいターミナルを開きます。
3. ブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 指示を更新する前に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 次の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習ごとに異なるため、必要な手順で説明します。

まず現在の指示ファイルを使ってプロンプトを送信します。後で指示を更新し、同じプロンプトを再送信して違いを確認します。

1. 前の演習で開いたエディタータブを閉じ、Copilot が必要なコンテキストだけを取得できるようにします。
2. `src/lib/publishers.ts` を開き、ヘルパーの配置先を示します。
3. Chat ビューのエージェント一覧で **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot はプロジェクトを調査し、ヘルパーファイルとテストなどを更新します。
6. 提案されたヘルパーは、データレイヤーの規約を定める `.github/instructions/drizzle.instructions.md` (これは `src/lib/*.ts` に適用されます) に従い、最初の引数に `db` クライアントを取り、型付きの発行元配列を返す関数になることを確認します。
7. 提案されたコードに TSDoc コメントとファイルレベルのコメントヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot は確率的に動作するため、指示がなくてもドキュメントコメントを追加することがあります。その場合も問題ありません。指示更新後の一貫性の向上を確認することが目的です。

## リポジトリ標準を追加する

`.github/copilot-instructions.md` は Copilot にプロジェクトレベルの情報を提供します。コードの提案を改善するため、リポジトリの標準を追加します。

1. `.github/copilot-instructions.md` を開き直します。
2. 27 行目付近の **Code formatting requirements** セクションを探します。生成されたヘルパーにドキュメントコメントがなかったのは、コード内ドキュメントのルールがまだないためです。
3. 既存の標準の直後に、次の Markdown を追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 指示ファイルはリポジトリレベルの `.github/copilot-instructions.md` に全体的な指針を記載するか、特定の言語、ファイル、タスク向けの `*.instructions.md` に記載できます。今回追加したドキュメントコメントのような全体標準には、リポジトリレベルのファイルが適しています。

## プロンプトを再実行して違いを確認する

1. Copilot Chat に戻り、**New Chat** を選択して会話を新しくします。
2. `src/lib/publishers.ts` を選択し、Copilot に対象ファイルを示します。
3. **同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案されたファイルの先頭に、次のようなコメントブロックが追加されたことを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 関数に次のような TSDoc コメントが追加されたことを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

新しいプロジェクト標準に従うよう Copilot をガイドし、次の演習で使う実際のコードに適用しました。

## 指示をコミットしてブランチをプッシュする

指示ファイルも、リポジトリのほかのアセットと同じようにソース管理で管理します。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して新しいターミナルを開きます。
2. 次のコマンドで指示とヘルパーの変更を確認します。

   ```bash
   git status
   ```

3. 指示の更新と、Copilot がヘルパーの基盤として変更したすべてのファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # git status に追加の関連ファイル (テストなど) が表示された場合は、それらも追加します。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. ブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、ドキュメント標準に関するバックログ Issue と関連付けて PR を作成します。GitHub ツールを使える Copilot に、Issue を探して PR を作成し、マージするよう依頼します。

> [!NOTE]
> Copilot が GitHub 上の操作を行う最初の演習です。プロジェクトのテンプレートでは GitHub ツールがワークスペースに設定済みです。Copilot が初めてツールを使う際に、VS Code から GitHub へのサインインを求められる場合があります。画面の指示に従って許可してください。後の演習で、この接続のしくみ (Model Context Protocol) を学びます。

1. Codespace の Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェント一覧で **Agent** が選択されていることを確認します。
3. 指示ファイルを更新する Issue を探し、現在のブランチから PR を作成して、指示の更新と発行元ヘルパーの基盤の両方を説明するよう Copilot に依頼します。

   ```plaintext
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot が Issue を見つけて PR を作成します。
5. GitHub の操作を許可するよう求められたら、コマンドを確認して適切に **Allow** を選択します。
6. PR が作成されたら、次のプロンプトでマージし、ローカルのブランチを `main` に戻します。

   ```plaintext
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. GitHub 操作やシェルコマンドの実行を許可するよう求められたら、内容を確認して適切に **Allow** を選択します。

GitHub Copilot の支援で PR を作成してマージしました。

## まとめと次のステップ

このプロジェクトで Copilot が指示ファイルからコンテキストを取得する方法を確認し、VS Code の Copilot Chat で次の作業を行いました。

- 既存の指示でコード生成プロンプトを実行しました。
- `.github/copilot-instructions.md` に全体標準を追加しました。
- 同じプロンプトを再実行し、新しい標準がコードに反映される様子を確認しました。
- 次の演習の基盤として、指示と発行元ヘルパーを `main` にコミットしました。

次は、[エージェントモード][next-lesson]で指示を適用し、コードベース全体に新しい機能を追加します。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [効果的なカスタム指示を作成するためのベストプラクティス][instructions-best-practices]
- [Copilot のカスタム指示を改善する 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム指示][personal-instructions]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://github.com/github/awesome-copilot
