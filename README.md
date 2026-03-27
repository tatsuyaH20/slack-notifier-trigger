# slack-notifier-trigger

[backlog-slack-notifier](https://github.com/tatsuyaH20/backlog-slack-notifier-) へ `repository_dispatch` イベントを送信するトリガーリポジトリです。
定期スケジュールまたは手動実行で各チームの Slack 通知を起動します。

## ワークフロー一覧

| ファイル            | 通知対象            | 自動実行タイミング   |
| ------------------- | ------------------- | -------------------- |
| `btob-frontend.yml` | BtoB フロントエンド | 毎週金曜日 11:00 JST |
| `btoc-design.yml`   | BtoC デザイン       | 毎週火曜日 09:00 JST |

## ワークフローの詳細

### Trigger BtoB Frontend Notifier (`btob-frontend.yml`)

毎週金曜日 11:00 JST にスケジュール実行され、`trigger-btob-frontend` イベントを dispatch します。

**手動実行時のパラメータ**

| パラメータ    | 説明                                                                | デフォルト |
| ------------- | ------------------------------------------------------------------- | ---------- |
| `target_week` | 対象週のオフセット（`0`=直前の木曜日〜次の木曜日、`-1`=その前の週） | `0`        |

> スケジュール実行時は `target_week` が自動的に `-1`（前週）になります。

---

### Trigger BtoC Design Notifier (`btoc-design.yml`)

毎週火曜日 09:00 JST にスケジュール実行され、`trigger-btoc-design` イベントを dispatch します。

**手動実行時のパラメータ**

| パラメータ | 説明                                                     | デフォルト |
| ---------- | -------------------------------------------------------- | ---------- |
| `dry_run`  | `true` にするとSlack送信をスキップしてペイロードのみ表示 | `true`     |

> スケジュール実行時は `dry_run` が自動的に `false` になります。

## セットアップ

### 必要なシークレット

| シークレット名         | 説明                                                                                                      |
| ---------------------- | --------------------------------------------------------------------------------------------------------- |
| `BACKLOG_NOTIFIER_PAT` | `backlog-slack-notifier` リポジトリへの `repository_dispatch` 送信権限を持つ GitHub Personal Access Token |

GitHub の **Settings > Secrets and variables > Actions** から登録してください。

## 手動実行

GitHub Actions の **Actions** タブから各ワークフローを選択し、**Run workflow** ボタンで手動実行できます。
