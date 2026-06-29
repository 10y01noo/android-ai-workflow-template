---
description: Claude command。要件から Android 実装プランを生成し .ai/tasks/<task-id>/plan.md に保存
argument-hint: <task-id> <やりたいこと>
---

`$ARGUMENTS` の先頭トークンを `<task-id>`、残りを要件として扱ってください。

## 入力検証

- `<task-id>` は必須。
- `<task-id>` は `^[a-z0-9]+(-[a-z0-9]+)*$` に一致する kebab-case のみ許可する。
- 現在 branch に応じて `feature/<task-id>` を準備する。
  - 現在 branch が `feature/<task-id>` の場合はそのまま続行する。
  - 現在 branch が `main` / `master` / `develop` で、`feature/<task-id>` が存在しない場合は `git switch -c feature/<task-id>` で作成して続行する。
  - 現在 branch が `main` / `master` / `develop` で、`feature/<task-id>` が存在する場合は `git switch feature/<task-id>` で移動して続行する。
  - 無関係な branch または detached HEAD の場合は停止し、現在 branch と期待 branch を報告する。ユーザー承認後だけ作成または移動する。
  - 未コミット変更が `git switch` を妨げる場合は強行せず、commit または stash を促す。
- `.ai/tasks/<task-id>/plan.md` が既に存在する場合は上書きせず、続行可否をユーザーへ確認する。

## 実行

`architect-agent` に以下を渡して plan を生成させてください。

- task-id: `<task-id>`
- 要件: `<task-id>` を除いた残りの `$ARGUMENTS`
- 保存先: `.ai/tasks/<task-id>/plan.md`
- 参照必須: `docs/PROJECT_GUIDELINES.md`

保存先: `.ai/tasks/<task-id>/plan.md`

agent が返した plan を command 側で `.ai/tasks/<task-id>/plan.md` に書き出してください。ディレクトリが無ければ作成してください。

対象コードは変更しないでください。


