---
description: 新しい Qiita 記事を限定共有 (private: true) として作成します
allowed-tools: Bash, Read, Edit
---

新しい Qiita 記事を作成します。

## 引数
- `$ARGUMENTS`: 記事の basename（例: my-first-article）

## 処理フロー

1. basename が未指定の場合、ユーザーに確認
2. `npx qiita new $ARGUMENTS` を実行
3. 生成された `public/$ARGUMENTS.md` を読み込み
4. フロントマッターの `private: false` を `private: true` に変更
5. 作成完了を報告

## 重要な制約

**記事は必ず限定共有 (private: true) として作成すること。**

Qiita CLI のデフォルトは `private: false` のため、必ず Edit ツールで書き換えること。
