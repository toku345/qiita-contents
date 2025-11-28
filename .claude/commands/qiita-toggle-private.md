---
description: 指定した記事の公開/限定共有状態を切り替えます（確認必須）
allowed-tools: Glob, Read, Edit
---

指定した Qiita 記事の公開状態を切り替えます。

## 引数
- `$ARGUMENTS`: 記事の basename または ファイルパス

## 処理フロー

1. 対象ファイルを特定（basename のみ → `public/$ARGUMENTS.md`）
2. フロントマッターから現在の `private` 値と `title` を読み取り
3. **必ず** AskUserQuestion ツールでユーザーに確認：
   - 記事タイトルを表示
   - 現在の状態（公開 or 限定共有）を明示
   - 切り替え後の状態を明示
4. ユーザーが承認した場合のみ、`private` の値を反転

## 確認ダイアログ例

```
header: "公開状態変更"
question: "「{title}」を【限定共有 → 公開】に変更しますか？"
options:
  - label: "変更する"
  - label: "キャンセル"
```

## 重要な制約

**ユーザー確認なしに公開状態を変更してはならない。**

特に `private: true` → `private: false`（限定共有 → 公開）は慎重に確認すること。
