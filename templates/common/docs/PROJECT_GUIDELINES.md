# PROJECT_GUIDELINES.md — {{APPLICATION_NAME}}

{{APPLICATION_NAME}} の Android 開発共通仕様です。AI agent は作業開始前に必ずこのファイルを読み、ユーザーの最新指示と矛盾しない範囲でこの内容を優先してください。

---

## デザイン仕様（Optional）

既存の design system や UI guideline がある Repository では、その文書を正とします。専用の `docs/DESIGN.md` を新設する必要はありません。

- 既存の design 文書がある場合: `{{DESIGN_SPEC_PATH}}` をその文書パスへ置換する。
- 既存の design 文書が残っていない場合: `{{DESIGN_SPEC_PATH}}` を `N/A` にし、既存画面、layout、resource、theme、style、component を source of truth とする。
- 既存 UI だけでは判断できない新規 UI が多い場合: `docs/DESIGN.example.md` を参考に任意の design 文書を作成する。
- design 文書を管理しない場合: `/design-command` / `$design-command` workflow は原則使わず、必要なタスクだけ `.ai/tasks/<task-id>/design.md` を作って判断を共有する。

UI 変更を行う場合は、既存画面、既存 resource、既存 component の visual language を優先し、テンプレート都合で design system を上書きしないでください。

---

## ロギング仕様

`{{LOGGER_CLASS}}` の使い方、ログレベル、メッセージフォーマットは `docs/LOGGING.md` を参照します。初期導入時は `docs/LOGGING.example.md` を `docs/LOGGING.md` にリネームし、logger 実装に合わせて更新してください。

---

## module 構成

```text
{{APPLICATION_NAME}}/
├── app/        # Application module。entry point と DI graph 統合
├── feature/    # UI 層。Fragment / ViewModel / Adapter / UiState / UiEvent
├── domain/     # business logic 層。UseCase interface / Repository interface / Model
├── data/       # data 層。Room / Retrofit / Repository implementation / DI binding
└── core/       # 共通 UI / utility。Extension / base class / lifecycle helper / logging
```

### 依存方向

```text
app -> feature -> domain <- data
app ->            domain <- data
          feature <- core
                    data <- core
```

- `app` は `feature`、`data`、`domain` に依存し、DI graph を統合する。
- `feature` は `domain` と `core` に依存する。
- `data` は `domain` と `core` に依存し、`domain` の Repository interface を実装する。
- `domain` は Android framework に依存しない純粋な Kotlin module にする。
- `core` は DI に依存しない共通 module とし、`feature` と `data` から利用する。

---

## 標準技術スタック

| カテゴリ | 標準 |
|---|---|
| 言語 | Kotlin |
| DI | Hilt |
| 画面遷移 | Navigation Component |
| View binding | ViewBinding |
| local DB | Room |
| network | Retrofit + OkHttp + Gson |
| image loading | Glide |
| async | Kotlin Coroutines + Flow |
| code generation | KSP |

移植先で差し替える場合は、`docs/CUSTOMIZATION_GUIDE.md` に従い role と guidelines の両方を更新してください。

---

## アーキテクチャパターン

### UseCase interface + implementation

- `domain` は UseCase を interface として定義する。
- UseCase implementation は別ファイルの `XxxUseCaseImpl` に置く。
- UseCase implementation の DI binding は `app` module の `UseCaseModule` に置く。
- Repository interface は `domain` に置く。
- Repository implementation は `data` の `XxxRepositoryImpl` に置く。
- Repository implementation の DI binding は `data` module の `RepositoryModule` に置く。

### UiState / UiEvent

- `UiState`: `sealed interface` で画面の表示状態を表現する。
- `UiEvent`: `SharedFlow` で一度だけ消費されるイベントを表現する。
- ViewModel は `StateFlow<UiState>` を公開する。
- Toast、navigation、snackbar など一度だけ処理するものがある場合は `SharedFlow<UiEvent>` も公開する。
- `UiEvent` が不要な画面では省略してよい。

### ViewModel state composition

複数の upstream Flow は `combine` で畳み込み、単一の `uiState` として公開します。複数 coroutine から同じ状態へ無秩序に直接書き込まないでください。

```kotlin
val uiState: StateFlow<XxxUiState> =
    combine(getXxxUseCase(), refreshState) { data, refresh ->
        toUiState(data, refresh)
    }.stateIn(
        viewModelScope,
        SharingStarted.WhileSubscribed(5_000L),
        XxxUiState.Loading,
    )
```

### Fragment pattern

- 通常の Fragment は `{{BASE_FRAGMENT_NAME}}` を継承する。
- DialogFragment 系は基底 Fragment の適用可否を個別判断する。
- ViewBinding は移植先で採用している仕組みに従い、view lifecycle に合わせて `onDestroyView` 時に解放する。
- RecyclerView Adapter は view lifecycle に合わせて解放する。
- `uiState` は lifecycle-aware に収集し `render()` に委譲する。
- `uiEvent` は lifecycle-aware に収集し `handleEvent()` に委譲する。

---

## build / test / lint

```bash
{{ASSEMBLE_DEBUG_TASK}}
{{UNIT_TEST_TASK}}
{{LINT_TASK}}
```

- Version Catalogs で dependency version を一元管理する。
- Room schema は migration 検証のため Git 管理する。
- SDK / dependency の更新判断は別タスクで扱い、実装タスクへ混ぜない。

---

## テスト方針

| テスト種別 | フレームワーク | 対象 |
|---|---|---|
| unit test | JUnit + MockK + Turbine | ViewModel / UseCase / Mapper |
| Android unit test | Robolectric | Android API に依存するテスト、Room |
| instrumentation | Espresso | UI integration |

- Flow のテストには Turbine を使用する。
- 実装変更に対応する既存テストがある場合は更新する。
- bug fix では可能な限り再発防止テストを追加する。
- テストを実行できなかった場合は理由を完了報告に記載する。
- テストを通すためだけに本来の仕様を変更しない。

---

## KDoc 規約

全 source class に KDoc を付けます。言語は移植先チームの標準に合わせます。日本語チームでは日本語を推奨します。

| 対象 | ルール |
|---|---|
| class / interface / object | 必須。役割と動作の要点を書く |
| method | 非 lifecycle method は必須。`onCreate` など lifecycle callback は省略可 |
| property / const | 必須。`companion object` 内の const も含む |

- `@param`: parameter がある場合は全 parameter 分を書く。
- `@return`: `Unit` 以外を返す場合に書く。
- `@property`: data class constructor parameter に使う。

---

## コーディング規約

- 既存の設計、命名、directory 構成を優先する。
- タスクに必要のない変更をしない。
- package root は `{{BASE_PACKAGE}}` とする。
- sealed 型を分岐する `when (uiState)` では全分岐を明示し、`else` で省略しない。
- `CancellationException` は必ず再スローする。
- public Flow の backing property には `private` を付ける。
- 不要な `!!` は使用しない。
- UI、ViewModel、UseCase、Repository の責務分担を維持する。
- XML / ViewBinding 標準構成を、依頼なく Compose / DataBinding へ変更しない。
- 新しい dependency は必要最小限にし、追加理由を明確にする。
- 無関係な refactoring、formatting、rename を行わない。

---

## ファイル配置の慣習

```text
feature/src/main/java/{{BASE_PACKAGE_PATH}}/feature/<feature-name>/
    <Feature>Fragment.kt
    <Feature>ViewModel.kt
    <Feature>UiState.kt
    <Feature>UiEvent.kt
    <Feature>ItemUiModel.kt
    <Feature>Adapter.kt
```

`{{BASE_PACKAGE_PATH}}` は `{{BASE_PACKAGE}}` の `.` を `/` に置換した path です。

---

## AI workflow

- `.ai/`、`.claude/`、`.agents/` の構造と相互参照は `docs/AI_WORKFLOW.md` を参照する。
- workflow は `<task-id>` を必須とし、`feature/<task-id>` branch と対応させる。
- plan は `.ai/tasks/<task-id>/plan.md` に保存する。
- design は optional。必要な場合だけ `.ai/tasks/<task-id>/design.md` に保存する。
- review は `.ai/tasks/<task-id>/review.md` に保存する。
- plan と、存在する場合の design は PR 中の共有成果物として扱う。
- review はローカルレビュー結果として扱い、`.gitignore` で `.ai/tasks/*/review.md` を除外する。






