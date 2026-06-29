# DECISIONS.md

Android AI workflow template の主要な設計判断です。

## 1. `.ai/instructions/roles/` を SSoT にする

Claude Code と Codex で拡張プリミティブは異なります。role 定義を `.ai/instructions/roles/` に集約し、`.claude/` と `.agents/` は薄いラッパにすることで、ツール差分と開発規約を分離します。

## 2. Workflow entrypoint は明示起動にする

`plan` / `design` / `implement` / `review` は task-id、branch、plan.md の存在確認などの gate を持ちます。自律起動すると副作用が大きいため、人間が明示起動する command / skill として扱います。

## 3. Android 規約補完 skill は gate と分離する

`android-conventions` は KDoc、logging、UiState / UiEvent の品質補完です。branch 操作や task-id 解釈を持たせず、Kotlin 編集時にだけ参照される軽いポインタにします。

## 4. Android architecture はテンプレート本体に残す

この template は技術非依存の AI workflow ではなく、Android Repository ですぐ使える実践テンプレートです。`app` / `feature` / `domain` / `data` / `core` の分割、Clean Architecture、MVVM、Hilt、Room、Retrofit、Flow、ViewBinding は標準構成として残します。

## 5. 製品固有語は変数化する

製品名、package、基底クラス名、logger 名は `{{...}}` 変数にします。Android 開発プラクティスは消さず、移植先で微修正できる状態を優先します。

## 6. Design spec は optional にする

既に design system を持つ Android Repository へ導入する場合、テンプレート側の `DESIGN.example.md` が邪魔になります。そのため design 文書は必須にせず、既存 design system がある場合は `{{DESIGN_SPEC_PATH}}` で参照するだけにします。

`/design-command` / `$design-command` は workflow として提供しますが、実行は任意です。UI 仕様を PR 中に AI と明示共有したい場合だけ `.ai/tasks/<task-id>/design.md` を作成します。

Design guide が残っていない Repository では `{{DESIGN_SPEC_PATH}}` を `N/A` にし、既存 UI 実装を source of truth とします。最初から `docs/DESIGN.md` を作らず、繰り返し発生した visual 判断だけを後から docs へ昇格します。

## 7. Branch policy は移植先で変更可能にする

標準テンプレートは `feature/<task-id>` を採用しますが、branch 運用は業務によって異なります。そのため branch policy は固定仕様ではなく、移植時に調整する前提の運用仕様として扱います。

ただし、`plan-command` だけが branch 作成を担当し、`design-command` / `implement-command` / `review-command` は branch 不一致時に停止する原則は標準として維持します。実装やレビューの途中で自動 branch 移動すると、差分照合と review の前提が崩れやすいためです。

## 8. 独立した public repository として配布する

このリポジトリは monorepo のサンプル配下から切り出し、独立した public template repository として配布する判断をした。

**理由**:

- monorepo 配下にある限り、採用側が `git clone` で単体取得できない。
- monorepo のリリースサイクルに従属し、LICENSE・公開可視性・community file が monorepo 方針に縛られる。
- 独立 repo にすることで、他の Android プロジェクトから容易に採用できる要件を満たせる。
- GitHub の template repository 機能（"Use this template"）と Release tag による版管理が可能になる。

**単一 repo を維持する（`-resources` 等の分割はしない）**:

現状 42 ファイルは小規模であり、`common` / `claude` / `codex` は `.ai/instructions/roles/` → ラッパという強い相互参照を持つ。
分割すると採用者が複数 repo を clone・版整合させる負担が増え、SSoT の利点が崩れる。
将来 resources が肥大化したら、その時点で分離を再検討する。初版は分離しない。

**移行方式: 新規 init（D-1）**:

monorepo の無関係な履歴が public 化時に混入するリスクを排除するため、新しい repo を `git init` して現ディレクトリ内容を初期コミットとする clean start を採用する。
開発履歴は monorepo 側に残る。

## 9. LICENSE を設けない

このリポジトリは知り合いへの共有レベルの public 公開であり、OSS としての配布・宣伝は意図していない。

LICENSE ファイルを設けない。GitHub の慣習上、LICENSE 不在は全権利留保（閲覧は可、使用・複製・配布・改変は不可）を意味する。

将来 OSS として広く公開する場合は MIT または Apache-2.0 を追加する。
