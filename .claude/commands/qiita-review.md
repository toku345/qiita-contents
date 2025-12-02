---
description: 指定したQiita記事の品質をレビューし、改善提案を行います
allowed-tools: Bash, Read, Grep, Skill
---

Qiita記事の品質レビューを実行します。

## 引数

- `$ARGUMENTS`: レビュー対象の記事ファイルパス（例: public/my-article.md）

## 処理フロー

1. 引数が未指定の場合、`public/` 配下の記事一覧を表示して選択を促す
2. `article-reviewer` エージェントを起動し、以下を実行：
   - `bun run lint:text` でtextlintチェック
   - `qiita-guidelines` スキルを参照してガイドライン適合チェック
   - タイトル・見出し構造の妥当性確認
   - タグ選定の適切性評価
3. レビュー結果を品質スコア付きで報告

## 使用するサブエージェント

`article-reviewer` (subagent_type='article-reviewer')

## 出力

- 品質スコア（0-100）
- 改善点リスト
- 強み（ポジティブフィードバック）
- 次のステップ
