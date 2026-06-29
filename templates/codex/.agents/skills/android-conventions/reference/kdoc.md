# KDoc reference

> 権威 SSoT: `docs/PROJECT_GUIDELINES.md` の KDoc 規約。

## 対象

| 対象 | ルール |
|---|---|
| class / interface / object | 必須。役割と動作の要点を書く |
| method | 非 lifecycle method は必須 |
| property / const | 必須 |

## tags

- `@param`: parameter がある場合は全 parameter 分を書く。
- `@return`: `Unit` 以外を返す場合に書く。
- `@property`: data class constructor parameter に使う。

## よく使う property

```kotlin
/** [{{LOGGER_CLASS}}] に渡すログ出力元。 */
private val {{LOG_SOURCE_PROPERTY}} = this

/** ViewBinding の参照。onDestroyView で解放される。 */
private var bindingRef by autoClearedViewLifecycle<XxxBinding>()

/** null 非許容の ViewBinding accessor。 */
private val binding get() = requireNotNull(bindingRef)
```
