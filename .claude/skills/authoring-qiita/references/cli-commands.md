# Qiita CLI コマンドリファレンス

## 基本コマンド

| コマンド | 説明 |
|----------|------|
| `bunx qiita new <basename>` | 新規記事の雛形を作成 |
| `bunx qiita preview` | ローカルプレビューサーバーを起動（port: 8888） |
| `bunx qiita publish` | 記事を Qiita に公開（CI 経由推奨） |

## ファイル構造

```
public/
├─ .remote/          # Qiita CLI 管理ディレクトリ（編集禁止）
└─ <basename>.md     # 記事ファイル
```

- ファイル名（basename）は一度決めると変更不可（Qiita 記事 ID と紐付く）
- `public/.remote/` は自動生成のため、手動編集禁止

## フロントマッター構造

```yaml
---
title: 記事タイトル
tags:
  - Tag1
  - Tag2
private: true    # 公開状態
updated_at: '...'
id: <qiita-article-id>
---
```
