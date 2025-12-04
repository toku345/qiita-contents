---
description: public/ 配下の記事に対して textlint を実行し、日本語を校正します
allowed-tools: Bash
---

Qiita記事をLintで校正します。

## 引数
- `$ARGUMENTS`: 特定の記事ファイルパス（省略時は全記事）

## 処理フロー

1. 引数ありの場合: `bun run lint:text $ARGUMENTS`
2. 引数なしの場合: `bun run lint:text`
3. 結果を報告

## 利用可能なLintコマンド

| コマンド | 実行内容 |
|----------|----------|
| `bun run lint` | textlint + markdownlint + link-check（全チェック） |
| `bun run lint:text` | textlint のみ |
| `bun run lint:md` | markdownlint のみ |
| `bun run lint:links` | リンク切れチェック |

## 有効なルール

### textlint（.textlintrc.json）

- `preset-ja-spacing`: 日本語のスペーシング
- `preset-ja-technical-writing`: 技術文書向けルール

### markdownlint（.markdownlint-cli2.jsonc）

- Qiita記事向けにカスタマイズ済み（行長制限無効、HTML要素許可など）

## 自動修正

エラーが多い場合、以下のコマンドを提案すること：

- `bun run lint:fix`: textlint + markdownlint 自動修正
- `bun run lint:text:fix`: textlint のみ自動修正
- `bun run lint:md:fix`: markdownlint のみ自動修正
