# LOGGING.example.md

`docs/LOGGING.md` の初期テンプレートです。移植時にこのファイルを `docs/LOGGING.md` へリネームし、実際の logger 実装へ合わせて更新してください。

## Logger

- Logger class: `{{LOGGER_CLASS}}`
- Log source property: `{{LOG_SOURCE_PROPERTY}}`
- Logger module: `core`

```kotlin
private val {{LOG_SOURCE_PROPERTY}} = this
```

## メッセージ形式

| 種別 | フォーマット |
|---|---|
| メソッド開始 | `[methodName] start` |
| メソッド終了 | `[methodName] end` |
| 外部呼び出し失敗 | `[methodName] <target> Failed <reason>` |
| 早期 return | `[methodName] end <reason>` |

## ログレベル

| 状況 | Level | Throwable |
|---|---|---|
| start / end / 正常分岐 | info | no |
| 想定内の失敗 | warning | no |
| 予期しない例外 | error | yes |
| `CancellationException` | ログなし | rethrow |

## 制約

- PII、トークン、request body、response body、秘密情報を含む可能性のある例外メッセージをログに出力しない。
- `domain` が Android framework 非依存で `core` に依存しない場合、`domain` 内で logger を使用しない。
- `CancellationException` は再 throw する。
- analytics / business event は構造化する。多数の呼び出し元でイベント名をハードコードしない。

## テスト上の注意

logger が Android API に触れる場合、JVM テストでは no-op sink を inject する。

