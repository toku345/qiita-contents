---
description: Qiita 記事のローカルプレビューサーバーを起動します (port: 8888)
allowed-tools: Bash
---

Qiita CLI のプレビュー機能を起動します。

## 処理

1. `npx qiita preview` をバックグラウンドで実行
2. [http://localhost:8888](http://localhost:8888) でアクセス可能になることを通知
3. 停止方法を案内

## 備考

- プレビューサーバーはバックグラウンドで動作
- 限定共有記事も表示可能（qiita.config.json 設定に依存）
