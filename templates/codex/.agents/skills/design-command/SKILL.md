---
name: design-command
description: Codex command。task-id を指定して Android 画面のビジュアル仕様を .ai/tasks/<task-id>/design.md に保存する。
---

## 入力確認

- ユーザー入力の先頭トークンを `<task-id>` として扱う。
- 現在 branch が `feature/<task-id>` でない場合は停止する。
- `.ai/tasks/<task-id>/plan.md` が存在しない場合は停止する。

## 実行

`designer-agent` agent skill に以下を渡して design を生成する。

- task-id: `<task-id>`
- 保存先: `.ai/tasks/<task-id>/design.md`
- 参照必須:
  - `docs/PROJECT_GUIDELINES.md`
  - `.ai/tasks/<task-id>/plan.md`
- design spec:
  - `{{DESIGN_SPEC_PATH}}` が `N/A` でなく存在する場合は読む。
  - `N/A` または存在しない場合は、既存 layout / Fragment / Adapter / resource / theme / style を確認し、既存 UI の一貫性を source として扱う。

生成された design を `.ai/tasks/<task-id>/design.md` に保存する。ディレクトリが無ければ作成する。

対象コードは変更しない。

