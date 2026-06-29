# UiState / UiEvent reference

> 権威 SSoT: `docs/PROJECT_GUIDELINES.md` の UiState / UiEvent、ViewModel、Fragment pattern。

## UiState

```kotlin
sealed interface XxxUiState {
    data object Loading : XxxUiState
    data class Success(val items: List<XxxItemUiModel>) : XxxUiState
    data object Empty : XxxUiState
    data class Error(val message: String) : XxxUiState
}
```

## UiEvent

```kotlin
sealed interface XxxUiEvent {
    data class ShowToast(val message: String) : XxxUiEvent
}
```

## ViewModel

- `StateFlow<XxxUiState>` を公開する。
- 一度だけ消費する event がある場合は `SharedFlow<XxxUiEvent>` を公開する。
- backing property は `private` にする。
- 複数 upstream Flow は `combine` で合成する。

## UI collection

Fragment / ViewBinding 構成では lifecycle-aware collection を使い、描画処理は `render()`、一度きり event は `handleEvent()` に委譲する。

Compose 構成では `collectAsStateWithLifecycle()` など、同等の lifecycle-aware API に置き換える。
