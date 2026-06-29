# Logging reference

> 権威 SSoT: `docs/LOGGING.md`。

## 基本

```kotlin
private val {{LOG_SOURCE_PROPERTY}} = this
```

`{{LOGGER_CLASS}}` 呼び出しでは `source = {{LOG_SOURCE_PROPERTY}}` を渡します。

## メッセージ形式

| 種別 | フォーマット |
|---|---|
| start | `[methodName] start` |
| end | `[methodName] end` |
| failed | `[methodName] <target> Failed <reason>` |
| early return | `[methodName] end <reason>` |

## ログレベル

| 状況 | Level |
|---|---|
| start / end | info |
| 想定内の失敗 | warning |
| 予期しない例外 | error with throwable |
| `CancellationException` | ログなし、rethrow |

## 制約

- `domain` が `core` 非依存の場合、`domain` で logger を使わない。
- PII、トークン、request body、response body をログへ出さない。
- Flow の値を丸ごと dump しない。
