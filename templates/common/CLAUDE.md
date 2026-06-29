# CLAUDE.md — {{APPLICATION_NAME}}

作業開始前に `docs/PROJECT_GUIDELINES.md` を必ず読むこと。同ファイルをこの Repository における共通開発仕様として扱う。

ユーザーの最新指示と `docs/PROJECT_GUIDELINES.md` が矛盾する場合は、ユーザーの最新指示を優先する。

## Claude Code rules

- `.ai/`、`.claude/`、`.agents/` の構造と相互参照は `docs/AI_WORKFLOW.md` を参照する。
- `.claude/skills/android-conventions/` は Android 規約の補完レイヤーであり、branch 操作や task-id gate を持たない。
- workflow command は `<task-id>` を必須とし、現在 branch が `feature/<task-id>` であることを確認する。
- `/plan-command` のみ、base branch から `feature/<task-id>` を作成してよい。
- 実装前に `.ai/tasks/<task-id>/plan.md` を必ず読む。
- 実装レビューを依頼された場合は `reviewer-agent` を使用する。
- review だけを依頼された場合は対象コードを変更しない。
- `CHANGES_REQUESTED` の修正を依頼された場合は `.ai/tasks/<task-id>/review.md` の指摘を確認して対応する。







