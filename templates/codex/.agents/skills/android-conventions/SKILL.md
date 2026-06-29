---
name: android-conventions
description: feature / domain / data / core module の Kotlin ファイルを新規作成・編集するときに自動起動する Android 規約補完レイヤー。KDoc、{{LOGGER_CLASS}} logging、UiState/UiEvent パターンを適用する。
---

# android-conventions

> 権威 SSoT: 規約全文は `docs/PROJECT_GUIDELINES.md` と `docs/LOGGING.md` にあります。この skill は参照トリガーとチェックリストを提供するポインタです。

## いつ起動するか

- `feature/`、`domain/`、`data/`、`core/` module 配下の Kotlin ファイルを生成・編集するとき
- KDoc、`{{LOGGER_CLASS}}` logging、UiState / UiEvent を追加・修正するとき

起動しないケース: workflow の task-id 検証、branch 操作、plan / review の gate。

## チェックリスト

### KDoc

- class / interface / object に KDoc がある。
- 非 lifecycle method に KDoc がある。
- property / const に KDoc がある。
- `@param`、`@return`、`@property` が必要に応じて付いている。

詳細は `reference/kdoc.md` を読む。

### Logging

- class top に `private val {{LOG_SOURCE_PROPERTY}} = this` がある。
- `domain` module では logger を使っていない。
- start / end / failed / early return の format が `docs/LOGGING.md` に沿っている。
- `CancellationException` をログせず再スローしている。
- PII、token、request / response body を出していない。

詳細は `reference/logging.md` を読む。

### UiState / UiEvent

- `UiState` は `sealed interface`。
- ViewModel は `StateFlow<UiState>` を公開する。
- 一度だけ消費する event は `SharedFlow<UiEvent>` にする。
- 複数 upstream Flow は `combine` で状態合成する。
- UI は lifecycle-aware に collect し `render()` / `handleEvent()` に委譲する。
- `when (uiState)` で `else` を使わない。

詳細は `reference/uistate.md` を読む。
