---
name: authoring-qiita
description: |
  Qiita記事の作成、編集、公開を支援する。新規記事作成時は必ず
  private: trueで開始し、公開状態変更時はユーザー確認を必須とする。
  「記事を書きたい」「公開して」「レビューして」「プレビュー」等の
  リクエスト時に使用。
---

# Qiita 記事作成

## クイックスタート

新規記事を作成する:

```bash
bunx qiita new <basename>
```

**重要**: 生成後、必ず `private: false` を `private: true` に変更してから執筆開始。

## 核心ルール

1. **新規記事は必ず `private: true` で開始**
2. **公開状態変更は必ずユーザー確認を取る**（AskUserQuestion 使用）

## ワークフロー

### 記事作成
1. `bunx qiita new <basename>` を実行
2. `private: true` に変更
3. 記事を執筆
4. `bun run lint:fix` で校正

### プレビュー
```bash
bunx qiita preview
```
ブラウザで http://localhost:8888 を開く。

### 公開状態変更
[references/safety-rules.md](references/safety-rules.md) を参照。

## 品質チェック

```bash
bun run lint        # 全チェック
bun run lint:fix    # 自動修正
```

## 参照資料

- **コミュニティガイドライン**: [references/community-guidelines.md](references/community-guidelines.md)
- **公開状態の安全要件**: [references/safety-rules.md](references/safety-rules.md)
- **CLI コマンド詳細**: [references/cli-commands.md](references/cli-commands.md)
