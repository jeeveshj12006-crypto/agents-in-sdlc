---
title: "実践的な開発"
slug: ja-jp/real-world-development
authors:
  - geektrainer
lastUpdated: 2026-09-16
---

現実的なシナリオ、完成したアプリケーション、共通のバックログを使い、ソフトウェア開発ライフサイクル全体で GitHub Copilot を活用します。変更を計画し、コードを作成し、動作をテストし、プルリクエストをレビューし、エージェントと作業を管理する流れを反復して体験します。

## シナリオ

架空の企業 Tailspin Toys に新しく加わった開発者として参加します。同社はボードゲームをテーマにしたクラウドファンディングサービスを提供しています。チームのバックログは GitHub Issue として登録済みで、すぐに着手できます。カテゴリーやページネーションなどの機能開発に加え、アクセシビリティやコーディング規約の改善も含まれています。

## 作業環境を選ぶ

GitHub Copilot は、普段使っている開発環境で利用できます。開発方法に合った環境を選択してください。どのワークショップも個別のセットアップから始まり、Tailspin Toys の共通シナリオを使用します。

### [GitHub Copilot CLI][cli]

ターミナルでエージェント型アシスタントを使います。Plan モードと Autopilot モード、指示、スキル、カスタムエージェント、Playwright MCP、Agent Merge、実用的なスラッシュコマンドを扱います。

### [GitHub Copilot app][app]

デスクトップアプリでエージェントセッションを並行して実行し、セッションモードを切り替え、キャンバスで共同作業を行い、GitHub Issue とプルリクエストを管理します。

### [GitHub Copilot cloud agent][cloud]

GitHub Issue を非同期エージェントに割り当て、作業をガイドして進行状況を確認し、エージェントが作成したプルリクエストをレビューします。

### [Visual Studio Code][vscode]

Visual Studio Code と GitHub Codespaces で GitHub Copilot を使います。エディターを離れずに、Copilot Chat のエージェントモード、MCP サーバー、カスタムエージェントを活用します。

[cli]: cli/
[app]: app/
[cloud]: cloud/
[vscode]: vscode/
