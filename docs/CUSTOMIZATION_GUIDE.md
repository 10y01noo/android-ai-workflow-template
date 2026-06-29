# CUSTOMIZATION_GUIDE.md

移植先 Android Repository に合わせてテンプレートを調整するためのガイドです。

## branch 運用を変える場合

標準テンプレートは `feature/<task-id>` branch を前提にします。業務の Git 運用が異なる場合は、導入時に必ず branch policy を調整してください。

変更対象:

- `templates/common/docs/AI_WORKFLOW.md`
- `templates/claude/.claude/commands/plan-command.md`
- `templates/claude/.claude/commands/design-command.md`
- `templates/claude/.claude/commands/implement-command.md`
- `templates/claude/.claude/commands/review-command.md`
- `templates/codex/.agents/skills/plan-command/SKILL.md`
- `templates/codex/.agents/skills/design-command/SKILL.md`
- `templates/codex/.agents/skills/implement-command/SKILL.md`
- `templates/codex/.agents/skills/review-command/SKILL.md`
- `templates/common/AGENTS.md`
- `templates/common/CLAUDE.md`

変更時の原則:

- `docs/AI_WORKFLOW.md` を branch policy の正とする。
- Claude commands / agents と Codex command skills / agent skills は同じ branch policy に揃える。
- `plan-command` だけが branch 作成を担当し、`design-command` / `implement-command` / `review-command` は branch 不一致時に停止する構造を維持する。
- trunk-based などで branch 作成しない場合は、`plan-command` の自動 branch 作成を無効化する。
- detached HEAD や無関係な branch では自動移動せず、人間確認を挟む。

## module 構成を変える場合

標準構成は `app` / `feature` / `domain` / `data` / `core` です。

小規模アプリで module を減らす場合:

- `feature` と `app` を統合する場合でも、View / ViewModel / UseCase / Repository の責務分担は維持する。
- `domain` を削る場合は、Repository interface と UseCase の置き場所を `docs/PROJECT_GUIDELINES.md` に明記する。
- `core` を削る場合は、logging / lifecycle helper / common UI の置き場所を明記する。

module を増やす場合:

- `core-ui`、`core-common`、`core-network` などへ分割してよい。
- 依存方向は内側へ向け、`domain` を Android framework から独立させる。
- `.ai/instructions/roles/logic-implementation-role.md` と `ui-implementation-role.md` の担当範囲を更新する。

## Compose を採用する場合

標準テンプレートは Fragment / XML / ViewBinding / Navigation Component を前提にします。Compose に変更する場合は次を修正してください。

- `docs/PROJECT_GUIDELINES.md` の Fragment / ViewBinding セクション
- `.ai/instructions/roles/ui-implementation-role.md`
- `.ai/instructions/roles/visual-design-role.md`
- `.claude/skills/android-conventions/reference/uistate.md`
- `.agents/skills/android-conventions/reference/uistate.md`

Compose にしても、UiState / UiEvent、ViewModel、UseCase、Repository の境界は維持します。

## 既存 design system がある場合

`docs/DESIGN.example.md` は使わず、既存の design system / Figma / UI guideline / component catalog を正としてください。

- `{{DESIGN_SPEC_PATH}}` を既存文書の path に置換する。
- `/design-command` / `$design-command` は必要な場合だけ実行する。
- 既存 design system が十分なら `.ai/tasks/<task-id>/design.md` を作らず、plan と実装だけで進めてよい。
- AI がテンプレート都合で色、余白、component style を上書きしないよう、`docs/PROJECT_GUIDELINES.md` に「既存 design system を優先」と明記する。

## Design guide が残っていない場合

既存 UI はあるが design guide が残っていない Repository では、最初から `docs/DESIGN.md` を作る必要はありません。

- `{{DESIGN_SPEC_PATH}}` は `N/A` にする。
- AI は既存画面、layout、resource、theme、style、component を source of truth として扱う。
- UI 変更タスクで判断を明文化したい場合だけ `.ai/tasks/<task-id>/design.md` を作る。
- 複数タスクで同じ visual 判断が繰り返されたら、その時点で `docs/DESIGN.md` または既存 docs へ昇格する。
- 新規 UI が多く、既存 UI だけでは判断できない場合だけ `docs/DESIGN.example.md` を使って最小限の design guide を作る。

## ライブラリを差し替える場合

| 標準 | 差し替え例 | 修正対象 |
|---|---|---|
| Hilt | Koin / manual DI | DI module 配置、role の DI 記述 |
| Room | SQLDelight / DataStore only | data 層、テスト方針 |
| Retrofit + OkHttp + Gson | Ktor / Moshi | data 層、network contract |
| Glide | Coil | UI role、依存管理 |
| XML / ViewBinding | Compose | UI role、optional design workflow、android-conventions |

## レビュー運用だけ導入する場合

`/plan-command` と `/review-command` だけでも使えます。その場合も `.ai/instructions/roles/architecture-role.md` と `implementation-review-role.md` は残し、`.ai/tasks/<task-id>/plan.md` をレビュー前提にしてください。

## 品質補完 skill を無効化する場合

`android-conventions` は補完レイヤーであり、workflow gate ではありません。不要な場合は `.claude/skills/android-conventions/` または `.agents/skills/android-conventions/` をコピーしないでください。







