---
description: Claude command。Android 画面のビジュアル仕様を .ai/tasks/<task-id>/design.md に保存
argument-hint: <task-id>
---

`$ARGUMENTS` の先頭トークンを `<task-id>` として扱ってください。

## 入力検証

- `<task-id>` は必須。
- 現在 branch が `feature/<task-id>` でない場合は停止する。
- `.ai/tasks/<task-id>/plan.md` が存在しない場合は停止する。

## 実行

`designer-agent` に以下を渡して design を生成させてください。

- task-id: `<task-id>`
- 設計プラン: `.ai/tasks/<task-id>/plan.md`
- design source: `{{DESIGN_SPEC_PATH}}` が `N/A` でなく存在する場合はその文書。`N/A` または存在しない場合は既存 layout / Fragment / Adapter / resource / theme / style。
- 保存先: `.ai/tasks/<task-id>/design.md`

`{{DESIGN_SPEC_PATH}}` が `N/A` でなく存在する場合は読みます。`N/A` または存在しない場合は、既存 layout / Fragment / Adapter / resource / theme / style を確認し、既存 UI の一貫性を source として扱ってください。

保存先: `.ai/tasks/<task-id>/design.md`

agent が返した design を command 側で `.ai/tasks/<task-id>/design.md` に書き出してください。ディレクトリが無ければ作成してください。

対象コードは変更しないでください。



