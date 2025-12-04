---
title: 「スライド作って」で本当にスライドができる — Marp × Claude Code 実践ガイド
tags:
  - Marp
  - ClaudeCode
  - Markdown
  - 自動化
  - プレゼンテーション
private: true
updated_at: '2025-12-04T12:30:37+09:00'
id: 11158328ca098957ff27
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

プレゼン資料を作成する際、以下のような課題に直面することが多いのではないでしょうか。

- **PowerPointを開くのが億劫**: ターミナルやエディタから離れる必要がある
- **操作方法を覚えるのが大変**: 多機能ゆえに使いこなせない
- **デザイン調整に時間を取られる**: コンテンツよりも見た目の調整に時間を取られる
- **差分管理が難しい**: 前のバージョンとの比較が困難

これらの課題を解決するツールとしてMarkdown形式のテキストファイルでスライドを作成可能な **Marp** があります。

さらに **Claude Code** と組み合わせることで、「自然言語での指示だけでスライド作成が完結する」ワークフローを構築できます。

## この記事で実現すること

本記事では、私が構築した [marp-slides リポジトリ](https://github.com/toku345/marp-slides) を例に、「自然言語での指示だけでスライド作成が完結する」ワークフローを紹介します。

| 従来の作業 | 自然言語での指示 |
|-----------|-----------------|
| ファイル作成・スライドの中身を記述 | 「<テーマ名>についてスライドを作成して」 |
| 目視チェック | 「スライドをレビューして」 |
| ビルドコマンド実行 | 「PDFに出力して」 |

Claude Codeと組み合わせることでMarpのコマンド・ルールを覚える必要なく、**意図を伝えるだけ**で作業が完了します。

## 想定読者

- Claude Codeの基本操作を理解している開発者
- スライド作成の効率化に関心がある方
- Agent Skills / Subagentsの実践的な活用例を探している方

# Marpとは

[Marp](https://marp.app/) は、Markdown形式でプレゼンテーションスライドを作成できるツールです。

## 基本的な仕組み

以下のようなフォーマットのMarkdownファイルを作成することでスライドを定義します。

- `---` で区切るだけでスライドが分割される
- テキストエディタで完結するため、IDEから離れる必要がない
- テキストベースのため、Gitでの差分管理やCI/CD連携も容易

```markdown:slides.md
---
marp: true
theme: default
paginate: true
---

# タイトルスライド

---

# 2枚目のスライド

- 箇条書きも使える
- コードブロックも対応
```

以下のようなコマンドでHTMLやPDF、PPTXとしてスライドを出力できます。

```console
# Marp CLIでのPDF出力例
$ npx @marp-team/marp-cli slides.md --pdf --output dist/slides.pdf
```

# Claude Code Agent Skills の導入

## Agent Skills とは

Claude CodeのAgent Skillsは、特定ドメインの知識をClaudeに提供する仕組みです。
`.claude/skills/<name>/skill.md` を用意することで、Claudeはその内容を参照しながら作業できます。

## Agent Skillsの定義例

```markdown:.claude/skills/marp/skill.md
---
name: marp
description: Marp (Markdown Presentation Ecosystem) を使用したスライド作成の完全ガイド。構文、テーマ、CLI、画像配置、トラブルシューティングをカバー。
---

# Marp スライド作成スキル

このスキルは、Marp (Markdown Presentation Ecosystem) を使用したプレゼンテーションスライドの作成、編集、出力の全工程をサポートします。

## Marp とは

Marp は、マークダウン形式でプレゼンテーションスライドを作成できるオープンソースのツールセットです。

### 主要コンポーネント

- **Marp Core**: マークダウンをスライドHTMLに変換するレンダリングエンジン
- **Marp CLI**: コマンドラインからスライドをビルド・エクスポートするツール
- **Marp for VS Code**: VS Code でライブプレビューしながら編集できる拡張機能
```

## marp スキルの役割

marp-slidesリポジトリでは以下のmarpスキルを定義しています。

| スキル | 役割 |
|-------------|------|
| [marp](https://github.com/toku345/marp-slides/blob/main/.claude/skills/marp/skill.md) | Marp構文やベストプラクティスの情報を提供 |

ここにMarpの基本ルールやベストプラクティスをまとめ、その情報を提供することで、Claudeは正しいMarp構文を生成したり、適切なコンテンツ量のスライドを作成することが可能になります。

# Subagents によるワークフロー自動化

Claude CodeのSubagentsは、特定のタスクに特化したエージェントです。
`.claude/agents/<name>.md` を用意することで、自然言語でトリガーされ、定義された手順を自動実行できます。

## Subagent の定義例

```markdown:.claude/agents/slide-creator.md
---
name: slide-creator
description: 新しいスライドを作成、プレゼンテーションファイルの新規作成、Marpファイルのテンプレート生成
tools: Read, Write, Glob, Bash
skills: marp
model: sonnet
---

# slide-creator エージェント

新規 Marp スライドファイルを作成するエージェント。

## 前提知識

詳細な Marp の構文・設定については、`marp` スキルを参照すること。
特に以下のセクションが重要:
- 基本的なマークダウン記法
- Front Matter 設定
- ディレクティブ
- 組み込みテーマ
- 画像の配置
- プロジェクト推奨構造
```

「Claude Codeの活用事例についてスライドを作成して」と入力するだけで、このフローが自動実行されます。
SubagentでSkillを使いたい場合はフロントマターに `skills: marp` のように記載する必要があるので注意してください。

## 3つのSubagentsの役割

marp-slidesリポジトリでは以下の3つのSubagentsを定義しています。

| Subagent | 起動例 | 役割 |
|----------|--------|------|
| [slide-creator](https://github.com/toku345/marp-slides/blob/main/.claude/agents/slide-creator.md) | 「スライドを作成して」 | 新規スライドをテンプレートから生成 |
| [slide-reviewer](https://github.com/toku345/marp-slides/blob/main/.claude/agents/slide-reviewer.md) | 「レビューして」 | 構文・コンテンツ量をチェック |
| [slide-builder](https://github.com/toku345/marp-slides/blob/main/.claude/agents/slide-builder.md) | 「PDFに出力して」 | HTML / PDF / PPTXにビルド |

これらのSubagentsを組み合わせることで、スライド作成からレビュー、ビルドまでの一連のワークフローを自然言語で完結させることができます。

# 動作確認環境

本記事の内容は以下の環境で動作確認しています。

- Marp CLI: v4.2.3
- Claude Code: v2.0.57
- Bun: v1.3.3
- OS: macOS 26.1

# 実践例

以下の手順で、自然言語ワークフローを体験できます。

## Step 1: リポジトリを準備

```bash
$ git clone https://github.com/toku345/marp-slides.git
$ cd marp-slides && bun install
```

## Step 2: Claude Codeを起動

```bash
$ claude
```

## Step 3: スライドを作成

以下のように入力してみてください。

```plaintext
6歳男児へのおすすめクリスマスプレゼント2つを提案する賑やかなスライドを作成して
```

slide-creatorが起動し、対話形式でテーマ選択 → テンプレート生成が行われます。

## Step 4: コンテンツを追加

```plaintext
プレゼントを1つ追加して
```

marpスキルを参照しながら、適切な構文・コンテンツ量でスライドが追加されます。

## Step 5: レビューを依頼

```plaintext
作成したスライドをレビューして
```

slide-reviewerが起動し、構文エラーやコンテンツ量の問題をレポートします。

## Step 6: PDFに出力

```plaintext
PDFに出力して
```

slide-builderが起動し、`dist/` 配下にPDFが生成されます。

## 実際の操作デモ

一連の流れは以下の動画でも確認できます。

<script src="https://asciinema.org/a/zDTiRAbhljkYMix2hBaYdghHo.js" id="asciicast-zDTiRAbhljkYMix2hBaYdghHo" async="true"></script>

出来上がったスライド: [christmas-gifts](https://toku345.github.io/marp-slides/christmas-presents-for-6yo-boy.html)

# 冒頭の課題はどう解決されたか

| 課題 | Marp × Claude Code での解決 |
|------|---------------------------|
| PowerPointを開くのが億劫 | ターミナルから離れずテキストで完結 |
| 操作方法を覚えるのが大変 | 自然言語で指示するだけ |
| デザイン調整に時間を取られる | Marpが自動で整形 |
| 差分管理が難しい | Git/GitHubで差分管理（[PR例](https://github.com/toku345/marp-slides/pull/25/files)） |

# Agent Skills / Subagents 活用のポイント

| 機能 | 役割 |
|------|------|
| **Agent Skills** | ドメイン知識（Marp構文など）を提供し、正確な出力を生成 |
| **Subagents** | 専門タスク（作成・レビュー・ビルド）を自然言語でトリガー |

# まとめ

複雑なコマンドを覚える必要なく、すべて自然言語での指示でスライド作成を完結させることができました。

MarpにはCSSを使ってカスタムテーマを作成する機能もあるため、さらに高度なデザイン調整も可能なので、もっと凝ったスライドも作成できます。

これを機に、ぜひMarp × Claude Codeの組み合わせを試してみてください。

# 参考資料

- [Marp 公式サイト](https://marp.app/)
- [Marp CLI](https://github.com/marp-team/marp-cli)
- [marp-slides リポジトリ](https://github.com/toku345/marp-slides)
- [Claude Code 公式ドキュメント](https://docs.claude.com/en/docs/claude-code)
  - [Claude Code Agent Skills](https://code.claude.com/docs/ja/skills)
  - [Claude Code Subagents](https://code.claude.com/docs/ja/sub-agents)
- [Claude Code スキル・サブエージェント攻略ガイド](https://zenn.dev/oligin/articles/7691926a83936a)
  - めちゃめちゃ参考にさせていただきました。
