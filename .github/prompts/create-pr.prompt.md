---
model: GPT-4.1
agent: agent
tools:
  [
    "search/codebase",
    "execute/getTerminalOutput",
    "execute/runInTerminal",
    "read/terminalLastCommand",
    "read/terminalSelection",
    "edit/editFiles",
  ]
description: develop 向けに PR を Draft で作成します。
---

# GitHub PR 作成プロンプト

gh コマンドを使って、Pull Request を Draft で作成するためのプロンプトです。

## ステップ1: 事前準備

1. 現在のブランチが `develop` でないことを確認
2. 変更がコミット済みであることを確認
3. リモートブランチにプッシュ済みであることを確認

## ステップ2: 変更内容分析

1. `git diff develop...HEAD` で変更差分を取得

## PR の内容

- タイトルと本文は日本語で簡潔に記述すること。
- PR のテンプレートは #file:../pull_request_template.md を参照すること。
- ターミナルで `date +%Y%m%d-%H%M` を実行し `.github/agents/yyyymmdd-hhmm-PR_BODY.md` ファイルを新規作成
  - 例）ファイル名の形式：`.github/agents/20251117-1430-PR_BODY.md` （日時は実行時刻）
- テンプレートの`## Ticket / Issue Number`と`## スクリーンショット`はテンプレートのまま記入すること。
- テンプレートの`<!-- このPRで何が変更されるのか、簡潔に説明してください -->`に従って、変更内容を記述すること。
- テンプレートの`## その他`は必要に応じて記入すること。
- 必ず `copilot` ラベルを付与してください（ラベルが存在しない場合は `gh` コマンドから作成すること）

## PR 作成手順

以下のコマンドをターミナルで実行し、PRをDraftで作成する：

> ※PRタイトル・本文はテンプレートに従い自動生成した内容を利用すること。

```bash
gh pr create --base develop --head <現在のブランチ名> --draft --label copilot --title "<PRタイトル>" --body-file <PR本文ファイルパス>
```

例：

```bash
gh pr create --base develop --head feature/creat-pr-by-copilot_test3 --draft --title "Copilot/Agent用の一時ファイル保存ルール・PR作成プロンプト・READMEのPR自動生成手順追加" --body-file .github/agents/20250815-1030-create-pr-pr-body.md
```
