# AGENTS.md — {{APPLICATION_NAME}}

作業開始前に `docs/PROJECT_GUIDELINES.md` を必ず読むこと。同ファイルをこの Repository における共通開発仕様として扱う。

ユーザーの最新指示と `docs/PROJECT_GUIDELINES.md` が矛盾する場合は、ユーザーの最新指示を優先する。

## Codex rules

- `.ai/`、`.claude/`、`.agents/` の構造と相互参照は `docs/AI_WORKFLOW.md` を参照する。
- `.agents/skills/android-conventions/` は Android 規約の補完レイヤーであり、branch 操作や task-id gate を持たない。
- 作業開始前に、依頼対象の既存実装と関連テストを確認する。
- command skill は `<task-id>` を必須とし、現在 branch が `feature/<task-id>` であることを確認する。
- `plan-command` skill のみ、base branch から `feature/<task-id>` を作成してよい。
- 実装前に `.ai/tasks/<task-id>/plan.md` を必ず読む。
- 変更は依頼達成に必要な最小範囲に限定する。
- 無関係な refactoring、formatting、rename を行わない。
- 既存 architecture や public API を、明示的な理由なく変更しない。
- 新しい dependency を、明示的な必要性なく追加しない。
- 生成ファイルや build artifacts を必要なく編集しない。
- review だけを依頼された場合は対象コードを変更しない。

## Review workflow

- 実装レビューを依頼された場合は `review-command` skill を使用する。
- `.ai/tasks/<task-id>/plan.md` と `git diff HEAD` を照合する。
- レビュー結果は `.ai/tasks/<task-id>/review.md` に保存する。
- 先頭は `VERDICT: PASS` または `VERDICT: CHANGES_REQUESTED` とする。







