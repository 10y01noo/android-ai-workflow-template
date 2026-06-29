---
name: review-command
description: Codex command。task-id を指定して Android 実装をレビューし、結果を .ai/tasks/<task-id>/review.md に保存する。
---

## 入力確認

- ユーザー入力の先頭トークンを `<task-id>` として扱う。
- 現在 branch が `feature/<task-id>` でない場合は停止する。
- `.ai/tasks/<task-id>/plan.md` が存在しない場合は、plan なしレビューを明示されているか確認する。

## 実行

`reviewer-agent` agent skill に以下を渡して review を生成する。

- task-id: `<task-id>`
- 参照必須:
  - `docs/PROJECT_GUIDELINES.md`
  - `.ai/tasks/<task-id>/plan.md`。plan なしレビューを明示された場合は、その理由と設計照合不可であることを review に含める。
  - `.ai/tasks/<task-id>/design.md` が存在する場合はその内容
  - `git status --short`
  - `git diff HEAD`
  - 未追跡ファイルがある場合はその内容
- 保存先: `.ai/tasks/<task-id>/review.md`

生成された review を `.ai/tasks/<task-id>/review.md` に保存する。ディレクトリが無ければ作成する。

対象コードは変更しない。

