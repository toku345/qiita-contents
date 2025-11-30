---
description: テーマを指定してQiita記事の構成案を生成します
allowed-tools: Read, Grep, Skill
---

Qiita記事の構成案を生成します。

## 引数

- `$ARGUMENTS`: 記事のテーマ（例: "Rubyで作るCLIツール"）

## 処理フロー

1. テーマが未指定の場合、ユーザーに確認
2. `article-composer` エージェントを起動し、以下を生成：
   - タイトル案（複数）
   - 推奨タグ
   - 見出し構成
   - 環境情報テンプレート
3. 構成案を提示し、フィードバックを求める

## 使用するサブエージェント

`article-composer` (subagent_type='article-composer')

## オプション

対象読者や技術スタックを指定する場合は、テーマと一緒に記述してください。

例:
- `/qiita-compose Rubyで作るCLIツール 初心者向け`
- `/qiita-compose Next.js 14のApp Router 中級者向け TypeScript`
