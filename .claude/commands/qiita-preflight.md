---
description: Qiita記事の公開前チェックを実行します（lint + リンク切れ + 禁止事項）
allowed-tools: Bash, Read, Grep, Skill
---

Qiita記事の公開前チェックを実行します。

## 引数

- `$ARGUMENTS`: チェック対象の記事ファイルパス（例: public/my-article.md）

## 処理フロー

1. 引数が未指定の場合、`public/` 配下の記事一覧を表示して選択を促す
2. `publish-checker` エージェントを起動し、以下を実行：
   - `bun run lint` で全Lintチェック
   - `bun run lint:links` でリンク切れチェック
   - 禁止事項チェック（著作権、NDA、個人情報等）
   - メタデータ確認（private フィールド、タイトル、タグ）
3. チェック結果をサマリー形式で報告

## 使用するサブエージェント

`publish-checker` (subagent_type='publish-checker')

## 出力

- 総合判定（公開可/要修正/公開不可）
- 各チェック項目の結果
- 修正が必要な項目のリスト
- 公開前の確認事項

## 重要

`private: false` への変更は、このコマンドでは自動実行されません。
公開する場合は `/qiita-toggle-private` コマンドを別途実行してください。
