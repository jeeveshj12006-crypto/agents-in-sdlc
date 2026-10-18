---
title: "演習 1 - カスタム指示 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

生成 AI を使うときはコンテキストが重要です。特定の方法でタスクを進めたい場合や、Copilot が知っておくべき背景情報がある場合は、コンテキストにアクセスできるようにします。[指示ファイル][instruction-files]を使うと、Copilot に作業内容だけでなく、その進め方も伝えられます。

この演習では、次の内容を学習します。

- リポジトリのカスタム指示とパス別の指示ファイルを通じて、プロジェクトのコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる方法を確認します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加します。

> [!NOTE]
> VS Code や CLI のハーネスとは異なり、この演習では変更前後のプロンプトを比較しません。Copilot cloud agent は GitHub Issue に対して非同期に作業するため、リアルタイムで影響を比較するのは困難です。このハーネスの後半で cloud agent が作成するプルリクエストをレビューし、指示ファイルの影響を確認します。

## 指示ファイル

### シナリオ

Tailspin Toys の開発チームには、開発慣行に関するガイドラインがあります。

- データレイヤーには必ず単体テストを用意します。
- UI はダークモードで、現代的なデザインにします。
- コードには TSDoc のドキュメントコメントを追加します。
- 各ファイルの先頭に、ファイルの役割を説明するコメントブロックを追加します。

指示ファイルを使い、Copilot がこれらの慣行に沿って作業できる情報を提供します。

### カスタム指示

カスタム指示を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードを得られます。コメントの方針や使用するライブラリなどを指定できます。リポジトリ全体に適用する指示と、特定のファイルやタスクに適用する指示を作成できます。

指示ファイルには、次の 2 種類があります。

- `.github/copilot-instructions.md`: リポジトリへの**すべての**依頼で Copilot に送信される単一の指示ファイルです。技術スタック、開発内容の概要、ベストプラクティスなど、ほとんどの依頼に関係する情報を記載します。
- `.github/instructions/*.instructions.md`: 特定のタスクやファイル種別に対する指示ファイルです。TypeScript や Astro などの言語、UI コンポーネントの作成、単体テストの追加などに関するガイドラインを記載できます。

> [!NOTE]
> IDE では、指示ファイルは Copilot Chat のコード生成にのみ使用され、コード補完や次の編集候補には使用されません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コードを生成する際にリポジトリレベルの指示ファイルと `applyTo` frontmatter を含む `*.instructions.md` ファイルの両方を使用します。
>
> Copilot は AGENTS.md や CLAUDE.md など、ほかの規格の指示ファイルも[サポートしています][custom-instructions-support]。

### 指示ファイルを管理するベストプラクティス

指示ファイルの作成についてすべて説明することはこのワークショップの範囲外ですが、サンプルプロジェクトには代表的な方法が含まれています。

- `copilot-instructions.md` には、構築しているもの、プロジェクトの構成、全体的なコーディング標準などのプロジェクトレベルの指針を記載します。
- `*.instructions.md` には、単体テスト、Astro コンポーネント、データレイヤーなどのファイル種別や、特定のタスクに対する指示を記載します。
- 自然な言葉を使い、明確に伝えます。コードの良い例と避ける例も示します。

指示ファイルの作成方法に決まりはありません。AI の使い方と同じように、試しながらプロジェクトに合った方法を見つけます。

> [!TIP]
> GitHub Copilot を使うプロジェクトには、充実した指示ファイルを用意することをお勧めします。このプロジェクトには、[UI 更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク向けの指示があります。
>
> Copilot は指示ファイルの生成も支援できます。VS Code の **Configure Chat → Generate Agent Instructions** や Copilot CLI の `/init` など、利用する環境によって手順が異なります。必要な場合は各レッスンで説明します。
>
> テンプレートや出発点を探す場合は、指示ファイル、カスタムエージェントなどが集まった [awesome-copilot][awesome-copilot] を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示を確認する

リポジトリには、コアの `copilot-instructions.md` と、タスクごとの `*.instructions.md` ファイルが含まれています。エディターまたは GitHub の Web UI で開き、内容を確認します。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの概要と **Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** にある **GitHub Actions Workflows** の指針を含め、Copilot とのあらゆるやり取りに適用されます。
3. `.github/instructions` フォルダーを開き、Astro ファイル、Drizzle データレイヤー、テストなどの指示を確認します。
4. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。この glob はリポジトリのルートからの相対パスで適用対象を指定します。たとえば、`**/*.test.ts` に一致する TypeScript テストファイルが対象です。
5. このプロジェクトの単体テスト作成に関する指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開いて末尾まで読みます。`unit-tests.instructions.md` などの指示ファイルや既存ファイルへのリンクを確認します。大きな指示を再利用可能な小さなファイルに分け、Copilot が参考にできる例を示せます (パスはリポジトリのルートではなく、指示ファイルからの相対パスです)。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** にはプロジェクトのコーディング標準が記載されていますが、コード内ドキュメントはまだ必須ではありません。次の手順で TSDoc コメントとファイルヘッダーを追加します。

## リポジトリ標準を追加する

この演習で編集するのは、コード内に TSDoc のドキュメントコメントとファイルレベルのコメントヘッダーを追加する全体ルールです。後の演習で Issue を割り当てると、cloud agent がこの指示に従います。

編集を始める前に、ブランチを作成します。cloud agent は Issue が対象とするブランチから指示を読み込みますが、変更をきれいにコミットするためにもブランチを使います。

1. Codespace のターミナルで、新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

`.github/copilot-instructions.md` は Copilot にプロジェクト情報を提供するファイルです。コーディング標準を記録して、コードの提案を改善します。

1. `.github/copilot-instructions.md` を開き直します。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。標準は記載されていますが、コード内ドキュメントに関するルールがないことを確認します。
3. 既存の標準の直後に、次の Markdown を追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 指示ファイルは、リポジトリレベルの `.github/copilot-instructions.md` に全体的な指針を記載する方法と、特定の言語、ファイル種別、タスク向けの `*.instructions.md` に記載する方法があります。今回追加したドキュメントコメントのような全体標準には、リポジトリレベルのファイルが適しています。

## 指示の更新をコミットし、プッシュしてマージする

Cloud agent は Issue の対象ブランチから指示ファイルを読み込みます。次の演習で Issue を割り当てると、Copilot は `main` からブランチを作成します。そのため、新しい指示を cloud agent に適用するには、変更を `main` にマージする必要があります。

1. 変更をステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを作成してマージします。次の演習で Issue を割り当てる際、cloud agent はこの指示を読み込みます。

> [!TIP]
> このワークショップで `main` に直接作業する場合は、ブランチ作成を省略して `main` にコミットできます。実際のプロジェクトでの作業に近づけるため、ここではブランチを使います。

## まとめと次のステップ

このプロジェクトで Copilot が指示ファイルのコンテキストを取得する方法を確認し、`.github/copilot-instructions.md` にリポジトリ全体の標準を追加しました。このハーネスの後半で cloud agent が作成する PR に、その標準が反映されます。

次は、[最初の Issue を Copilot cloud agent に割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [効果的なカスタム指示を作成するためのベストプラクティス][instructions-best-practices]
- [Copilot のカスタム指示を改善する 5 つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://awesome-copilot.github.com/
