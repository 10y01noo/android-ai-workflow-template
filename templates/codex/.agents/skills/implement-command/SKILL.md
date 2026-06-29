---
name: implement-command
description: Codex command。task-id を指定して .ai/tasks/<task-id>/plan.md と必要に応じて design.md / review.md に従って Android 実装を行う。
---

## 入力確認

- ユーザー入力の先頭トークンを `<task-id>` として扱う。
- 現在 branch が `feature/<task-id>` でない場合は停止する。
- `.ai/tasks/<task-id>/plan.md` が存在しない場合は停止する。
- `docs/PROJECT_GUIDELINES.md`、plan、design / review が存在する場合は読む。

## 担当振り分け

plan の変更範囲に応じて agent skill を呼び分ける。

- logic 層のみ: `implementer-agent`
- UI 層のみ: `implementer-ui-agent`
- 両方: `implementer-agent` -> `implementer-ui-agent` の順

## 実装ルール

- plan の step 順に進める。
- 変更範囲は必要最小限にする。
- review が `VERDICT: CHANGES_REQUESTED` の場合は未対応指摘を優先する。
- 可能な範囲で `{{ASSEMBLE_DEBUG_TASK}}`、`{{UNIT_TEST_TASK}}`、`{{LINT_TASK}}` を実行する。

