# CLAUDE.md

Guidance for Claude Code (claude.ai/code) working in this repository.

## Overview

Qiita記事を管理するリポジトリ。[Qiita CLI](https://github.com/increments/qiita-cli)を使用して記事を作成・プレビュー・公開する。

## Commands

```bash
# Linting
bun run lint           # textlint + markdownlint + link-check すべて実行
bun run lint:text      # textlint のみ
bun run lint:md        # markdownlint のみ
bun run lint:links     # リンク切れチェック

# Auto-fix
bun run lint:fix       # textlint + markdownlint 自動修正
bun run lint:text:fix  # textlint 自動修正
bun run lint:md:fix    # markdownlint 自動修正

# Qiita CLI
bunx qiita new <basename>  # 新規記事作成
bunx qiita preview         # ローカルプレビュー (port: 8888)
bunx qiita publish         # 記事公開（CI経由推奨）
```

## Architecture

```
public/
├── .remote/           # Qiita CLI管理（自動生成、編集禁止）
└── *.md               # 記事ファイル（ファイル名 = Qiita記事ID）
```

### 記事ファイル構造

各記事はYAMLフロントマッターを持つMarkdownファイル。

```yaml
---
title: 記事タイトル
tags:
  - Tag1
  - Tag2
private: true          # true=限定共有, false=公開
updated_at: '...'
id: <qiita-article-id>
---
```

## Workflow

### 記事作成

1. `bunx qiita new <basename>` で雛形生成
2. **必ず `private: true` に変更してから執筆開始**（デフォルトは`false`）
3. `bunx qiita preview` で確認
4. `bun run lint:fix` で校正

### 公開フロー

- mainブランチへのpushで自動公開（`.github/workflows/publish.yml`）
- `QIITA_TOKEN` シークレットが必要

## Linting Rules

### textlint (`.textlintrc.json`)

- `preset-ja-spacing`: 日本語スペーシング
- `preset-ja-technical-writing`: 技術文書向けルール

### markdownlint (`.markdownlint-cli2.jsonc`)

Qiita記事向けにカスタマイズ済み（行長制限無効、HTML要素許可など）

### 除外対象 (`.textlintignore`)

- `public/.remote/**`
- 一部の既存記事（レガシー）

## Claude Code Commands

このリポジトリには専用のスラッシュコマンドが定義されている。

| コマンド | 説明 |
|---------|------|
| `/qiita-new <basename>` | 新規記事作成（自動で`private: true`に設定） |
| `/qiita-preview` | ローカルプレビューサーバー起動 |
| `/qiita-lint [file]` | textlintによる日本語校正 |
| `/qiita-toggle-private <file>` | 公開/限定共有の切り替え（確認必須） |
| `/qiita-review <file>` | 記事品質レビュー（Subagent使用） |
| `/qiita-compose <theme>` | 記事構成案の生成（Subagent使用） |
| `/qiita-preflight <file>` | 公開前チェック（Subagent使用） |

## Claude Code Skills

| スキル | 説明 |
|--------|------|
| `qiita-guidelines` | 記事作成時にQiitaコミュニティガイドラインと品質要件を参照 |

記事執筆・レビュー・公開準備時に自動適用される。

## Claude Code Subagents

専門的なタスクを実行するサブエージェントが定義されている。

| Subagent | 用途 | コマンド |
|----------|------|----------|
| `article-reviewer` | 記事品質レビュー | `/qiita-review <file>` |
| `article-composer` | 記事構成支援 | `/qiita-compose <theme>` |
| `publish-checker` | 公開前チェック | `/qiita-preflight <file>` |

### 起動条件

自然言語での依頼時も適切なサブエージェントを使用すること：

- 「記事をレビューして」「品質チェック」→ `article-reviewer` を使用
- 「構成を考えて」「アウトラインを作成」→ `article-composer` を使用
- 「公開前チェック」「公開準備」→ `publish-checker` を使用

## Key Constraints

1. **新規記事は必ず `private: true` で作成する**
2. `public/.remote/` は自動生成ディレクトリのため編集禁止
3. 公開状態の変更（`private` フィールド）は慎重に行う
