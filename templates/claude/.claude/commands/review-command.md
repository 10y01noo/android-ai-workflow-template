---
description: Claude command。Android 実装をレビューし .ai/tasks/<task-id>/review.md に保存
argument-hint: <task-id>
---

`$ARGUMENTS` の先頭トークンを `<task-id>` として扱ってください。

## 入力検証

- `<task-id>` は必須。
- 現在 branch が `feature/<task-id>` でない場合は停止する。
- `.ai/tasks/<task-id>/plan.md` が存在しない場合は、plan なしレビューを明示されているか確認する。

## 実行

`reviewer-agent` に以下を渡して review を生成させてください。

- task-id: `<task-id>`
- 設計プラン: `.ai/tasks/<task-id>/plan.md` が存在する場合はその内容。plan なしレビューを明示された場合は plan 不在理由。
- design: `.ai/tasks/<task-id>/design.md` が存在する場合のみ。
- project guidelines: `docs/PROJECT_GUIDELINES.md`
- 変更状況: `git status --short`
- 差分: `git diff HEAD`
- 未追跡ファイル: `git status --short` で検出した対象ファイルの内容

保存先: `.ai/tasks/<task-id>/review.md`

agent が返した review を command 側で `.ai/tasks/<task-id>/review.md` に書き出してください。ディレクトリが無ければ作成してください。

レビューだけを依頼された場合は対象コードを変更しないでください。



