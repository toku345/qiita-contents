---
description: public/ 配下の記事に対して textlint を実行し、日本語を校正します
allowed-tools: Bash
---

Qiita記事をtextlintで校正します。

## 引数
- `$ARGUMENTS`: 特定の記事ファイルパス（省略時は全記事）

## 処理フロー

1. 引数ありの場合: `npx textlint $ARGUMENTS`
2. 引数なしの場合: `npx textlint public/`
3. 結果を報告

## 有効なルール（.textlintrc.json）

- `preset-ja-spacing`: 日本語のスペーシング
- `preset-ja-technical-writing`: 技術文書向けルール

## 自動修正

エラーが多い場合、`--fix` オプションの使用を提案すること。
