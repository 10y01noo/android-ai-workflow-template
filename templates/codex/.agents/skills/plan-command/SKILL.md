---
name: plan-command
description: Codex command。task-id を指定して Android 実装プランを生成し、.ai/tasks/<task-id>/plan.md に保存する。
---

## 入力確認

- ユーザー入力の先頭トークンを `<task-id>`、残りを要件本文として扱う。
- `<task-id>` は `^[a-z0-9]+(-[a-z0-9]+)*$` に一致すること。
- 現在 branch に応じて `feature/<task-id>` を準備する。
  - 現在 branch が `feature/<task-id>` の場合はそのまま続行する。
  - 現在 branch が `main` / `master` / `develop` で、`feature/<task-id>` が存在しない場合は `git switch -c feature/<task-id>` で作成して続行する。
  - 現在 branch が `main` / `master` / `develop` で、`feature/<task-id>` が存在する場合は `git switch feature/<task-id>` で移動して続行する。
  - 無関係な branch または detached HEAD の場合は停止し、現在 branch と期待 branch を報告する。ユーザー承認後だけ作成または移動する。
  - 未コミット変更が `git switch` を妨げる場合は強行せず、commit または stash を促す。
- `.ai/tasks/<task-id>/plan.md` が既に存在する場合は上書き確認する。

## 実行

`architect-agent` agent skill に以下を渡して plan を生成する。

- task-id: `<task-id>`
- 要件: `<task-id>` を除いた残りのユーザー入力
- 保存先: `.ai/tasks/<task-id>/plan.md`
- 参照必須: `docs/PROJECT_GUIDELINES.md`

生成された plan を `.ai/tasks/<task-id>/plan.md` に保存する。ディレクトリが無ければ作成する。

対象コードは変更しない。

