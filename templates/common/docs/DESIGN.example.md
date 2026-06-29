# DESIGN.example.md

任意で使う デザイン仕様テンプレートです。

既に デザインシステム、Figma 運用、UI ガイドライン、コンポーネントカタログ がある Repository では、このファイルを使う必要はありません。既存文書を `{{DESIGN_SPEC_PATH}}` として参照してください。

デザインガイドが残っていないが既存 UI はある Repository では、まず `{{DESIGN_SPEC_PATH}}` を `N/A` にし、既存 UI を判断基準にしてください。

新規 UI が多く、既存 UI だけでは判断できない場合だけ、このファイルをコピーまたはリネームして使ってください。

## ビジュアル方針

- デザインの方向性: {{DESIGN_DIRECTION}}
- 画面密度と余白感: {{UI_DENSITY}}
- デザインキーワード: {{DESIGN_KEYWORDS}}

## カラーパレット

| トークン | 値 | 用途 |
|---|---|---|
| `color_primary` | `{{COLOR_PRIMARY}}` | ブランド表現 / 主要アクション |
| `color_error` | `{{COLOR_ERROR}}` | エラー / 破壊的アクション |
| `text_primary` | `{{TEXT_PRIMARY}}` | 本文テキスト |
| `text_secondary` | `{{TEXT_SECONDARY}}` | 補助テキスト |
| `background` | `{{BACKGROUND}}` | 画面背景 |
| `surface` | `{{SURFACE}}` | カード / ツールバー / ダイアログ |
| `border` | `{{BORDER}}` | 区切り線 / 入力欄の枠線 |

## タイポグラフィ

| 役割 | サイズ | 太さ | 行の高さ |
|---|---|---|---|
| 見出し | `{{TEXT_HEADING_SIZE}}` | `{{TEXT_HEADING_WEIGHT}}` | `{{TEXT_HEADING_LINE_HEIGHT}}` |
| 本文 | `{{TEXT_BODY_SIZE}}` | `{{TEXT_BODY_WEIGHT}}` | `{{TEXT_BODY_LINE_HEIGHT}}` |
| キャプション | `{{TEXT_CAPTION_SIZE}}` | `{{TEXT_CAPTION_WEIGHT}}` | `{{TEXT_CAPTION_LINE_HEIGHT}}` |

## 余白

一貫した余白スケールを使ってください。例: `4 / 8 / 12 / 16 / 24 / 32 / 48 / 64` dp。

## コンポーネント規約

- ボタン: {{BUTTON_RULES}}
- 入力欄: {{INPUT_RULES}}
- カード: {{CARD_RULES}}
- リスト: {{LIST_RULES}}

## アクセシビリティ

- タップ領域は 48dp 以上を推奨し、最低でも 44dp を確保する。
- 状態を色だけで表現しない。
- 意味のある画像やアイコンボタンには `contentDescription` を設定する。
- 文字サイズを大きくした場合の表示崩れを確認する。

## UiState 別の表示方針

| 状態 | 表示方針 |
|---|---|
| Loading | {{LOADING_DISPLAY}} |
| Success | {{SUCCESS_DISPLAY}} |
| Empty | {{EMPTY_DISPLAY}} |
| Error | {{ERROR_DISPLAY}} |
| Offline | {{OFFLINE_DISPLAY}} |





