---
description: Claude command。.ai/tasks/<task-id>/plan.md と必要に応じて design.md / review.md に従って Android 実装
argument-hint: <task-id>
---

`$ARGUMENTS` の先頭トークンを `<task-id>` として扱ってください。

## 入力検証

- `<task-id>` は必須。
- 現在 branch が `feature/<task-id>` でない場合は停止する。
- `.ai/tasks/<task-id>/plan.md` が存在しない場合は停止する。

## 実行

`.ai/tasks/<task-id>/plan.md` を読み、変更範囲に応じて以下の agent を呼び分けて実装してください。

- logic 層: `implementer-agent`
- UI 層: `implementer-ui-agent`
- 両方: `implementer-agent` -> `implementer-ui-agent` の順

`.ai/tasks/<task-id>/review.md` が `VERDICT: CHANGES_REQUESTED` の場合は、未対応指摘を優先してください。

agent へ渡す材料:

- task-id: `<task-id>`
- 設計プラン: `.ai/tasks/<task-id>/plan.md`
- review 指摘: `.ai/tasks/<task-id>/review.md` が `VERDICT: CHANGES_REQUESTED` の場合のみ
- UI 実装時の design: `.ai/tasks/<task-id>/design.md` が存在する場合のみ。存在しない場合は既存 UI と `{{DESIGN_SPEC_PATH}}` の扱いに従う。

可能な範囲で `{{ASSEMBLE_DEBUG_TASK}}`、`{{UNIT_TEST_TASK}}`、`{{LINT_TASK}}` を実行してください。


