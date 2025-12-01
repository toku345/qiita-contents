---
title: Marp × Claude Code で実現する「自然言語で完結する」スライド作成ワークフロー
tags:
  - Marp
  - Markdown
  - ClaudeCode
  - プレゼンテーション
  - Bun
private: true
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

## 従来のスライド作成の課題

エンジニアがプレゼン資料を作成する際、以下のような課題に直面することが多いのではないでしょうか。

- **PowerPointでのデザイン調整が煩雑**: コンテンツよりも見た目の調整に時間を取られる
- **バージョン管理が困難**: バイナリ形式のためGit管理に不向き、差分が追えない
- **コードブロックの扱いが面倒**: シンタックスハイライトの設定に手間がかかる

これらの課題を解決するツールとして **Marp** がありますが、さらに **Claude Code** と組み合わせることで、「自然言語での指示だけでスライド作成が完結する」ワークフローを構築できます。

## この記事で実現すること

本記事では、私が構築した [marp-slides リポジトリ](https://github.com/toku345/marp-slides) を例に、以下を解説します。

| 従来の作業 | 自然言語での指示 |
|-----------|-----------------|
| ファイル作成・テンプレート記述 | 「テーマ名についてスライドを作成して」 |
| lint実行・目視チェック | 「スライドをレビューして」 |
| ビルドコマンド実行 | 「PDFに出力して」 |

コマンドを覚える必要なく、**意図を伝えるだけ**で作業が完了します。

## 想定読者

- Claude Codeの基本操作を理解している開発者
- スライド作成の効率化に関心があるエンジニア
- Skills / Subagentsの実践的な活用例を探している方

# 環境情報

| ソフトウェア | バージョン |
|-------------|-----------|
| OS          | macOS 14.x |
| Bun         | 1.3.2 |
| @marp-team/marp-cli | 3.4.0 |
| Claude Code | latest |
| Playwright MCP | - |

※ Playwright MCPは視覚的に検証する場合に必要です。

## セットアップ手順

### 前提条件

- Node.js 18.x以上、またはBun 1.3.x以上
- Claude Codeがインストール済み

### リポジトリの準備

```bash
# リポジトリをクローン
git clone https://github.com/toku345/marp-slides.git
cd marp-slides

# 依存関係をインストール
bun install

# プレビューサーバーを起動して動作確認
bun run preview
# → http://localhost:8080 でスライドを確認
```

これで基本的なセットアップは完了です。Claude Codeを起動すれば、自然言語での操作が可能になります。

# Marpとは

[Marp](https://marp.app/) は、Markdown形式でプレゼンテーションスライドを作成できるツールです。

## 基本的な仕組み

```markdown
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

`---` で区切るだけでスライドが分割されます。テキストエディタで完結するため、IDEから離れる必要がありません。

## Git完全対応

テキストベースのため、以下のメリットがあります。

- **差分が可視化される**: 何を変更したか一目瞭然
- **ブランチ戦略が適用可能**: feature branchでスライドを作成、レビュー後にマージ
- **CI/CD連携**: push時に自動ビルド・デプロイ

## 3種類の組み込みテーマ

| テーマ | 特徴 |
|-------|------|
| `default` | シンプルでビジネス向け |
| `gaia` | 視覚的に華やかなプレゼン向け |
| `uncover` | モダンでミニマルなデザイン |

# リポジトリ構造

今回構築した [marp-slides](https://github.com/toku345/marp-slides) リポジトリの構造を紹介します。

```
marp-slides/
├── slides/              # Markdownスライドファイル
│   └── images/          # スライド用画像
├── themes/              # カスタムCSSテーマ
├── dist/                # ビルド出力（HTML/PDF/PPTX）
└── .claude/
    ├── agents/          # Subagent定義
    │   ├── slide-builder.md
    │   ├── slide-creator.md
    │   └── slide-reviewer.md
    └── skills/marp/     # Marpスキル
        └── skill.md
```

## ディレクトリの役割

| ディレクトリ | 説明 |
|-------------|------|
| `slides/` | スライドの執筆場所。画像は `images/` 配下に配置 |
| `themes/` | カスタムCSSでデザインを拡張 |
| `dist/` | ビルド成果物（`.gitignore` で除外推奨） |
| `.claude/agents/` | Subagent定義ファイル |
| `.claude/skills/marp/` | Marp構文リファレンス |

# Claude Code Skill の導入

## Skill とは

Claude CodeのSkillは、特定ドメインの知識をClaudeに提供する仕組みです。`.claude/skills/<name>/skill.md` に配置することで、Claudeがその内容を参照しながら作業します。

## marp スキルの役割

今回作成した `marp` スキルには以下が含まれています。

- **Marp構文リファレンス**: Front Matter、ディレクティブ、スライド分割
- **テーマ・レイアウト**: `lead` / `invert` クラス、背景画像設定
- **画像配置**: 通常画像、背景画像、マルチカラムレイアウト
- **数式サポート**: KaTeXによる数式表示
- **トラブルシューティング**: よくある問題と解決策

## 効果

Skillを導入することで、以下が実現します。

- Marp構文を正確に生成（間違った構文を出力しない）
- レイアウト崩れを防止する適切なコンテンツ量の提案
- プロジェクト固有の規約（画像配置場所など）の遵守

# Subagents によるワークフロー自動化

Claude CodeのSubagentは、特定のタスクに特化したエージェントです。自然言語でトリガーされ、定義された手順を自動実行します。

## slide-creator: スライド新規作成

### 役割

新しいスライドファイルをプロジェクト規約に沿って生成します。

### 自然言語での起動例

```
「新しいスライドを作成して」
「Claude Codeの活用事例についてスライドを作成」
「gaia テーマでプレゼンを新規作成」
```

### 実行内容

1. テーマの選択（default / gaia / uncover）
2. Front Matterの自動記述
3. タイトル・アジェンダ・まとめスライドのテンプレート生成
4. `slides/` 配下への保存

## slide-reviewer: 品質チェック

### 役割

作成したスライドの品質を多角的にレビューします。

### 自然言語での起動例

```
「スライドをレビューして」
「marp-tutorial.md の品質チェックをお願い」
「プレゼン資料に問題がないか確認」
```

### チェック項目

**テキストベースチェック**

- Front Matterの妥当性（`marp: true` の有無、テーマ名の正当性）
- 1スライドあたりのコンテンツ量
- 画像パスの存在確認

**視覚的検証（Playwright MCP使用）**

- レイアウト崩れの検出
- テキストオーバーフローの検出
- フォントサイズの一貫性

## slide-builder: マルチフォーマット出力

### 役割

スライドをHTML / PDF / PPTXにビルドします。

### 自然言語での起動例

```
「PDFに出力して」
「スライドをビルド」
「HTMLとPDF両方出力して」
```

### 出力フォーマット

| 形式 | 用途 | コマンド |
|------|------|---------|
| HTML | Web公開、プレビュー | `bun run build` |
| PDF | 配布、印刷 | `marp --pdf` |
| PPTX | PowerPoint互換 | `marp --pptx` |

# 実践例：自然言語でのスライド作成フロー

実際のワークフローを示します。

## ステップ1: 新規作成

```
ユーザー: 「Claude Codeの活用事例についてスライドを作成して」
```

slide-creatorが起動し、以下を実行します。

- テーマ選択の確認
- Front Matter生成
- テンプレート作成
- `slides/claude-code-examples.md` として保存

## ステップ2: コンテンツ追加

```
ユーザー: 「Subagentsの説明スライドを3枚追加して」
```

marpスキルを参照しながら、適切な構文でスライドを追加します。1スライドあたりの文字量も自動調整されます。

## ステップ3: レビュー

```
ユーザー: 「スライドをレビューして」
```

slide-reviewerが起動し、以下をチェックします。

- 構文エラーの検出
- コンテンツ量の適正判断
- （Playwright MCP有効時）視覚的な崩れ検出

レポート形式で問題点が報告されます。

## ステップ4: ビルド

```
ユーザー: 「PDFに出力して」
```

slide-builderが起動し、`dist/` 配下にPDFを生成します。

## ポイント

**一度もコマンドを打っていない**点に注目してください。すべて自然言語での指示で完結しています。

# Playwright MCP による視覚的検証

## なぜ視覚的検証が必要か

Markdownベースのスライドでも、以下の問題は実際にレンダリングしないと検出できません。

- テキストがスライドからはみ出している
- 画像とテキストが重なっている
- フォントサイズが小さすぎて読めない

## Playwright MCP の導入

[Playwright MCP](https://github.com/microsoft/playwright-mcp) を設定することで、slide-reviewerが視覚的検証を行えるようになります。

:::note info
視覚的検証はオプション機能です。Playwright MCPを導入しなくても、テキストベースのレビュー（構文チェック、コンテンツ量確認など）は実行可能です。
:::

### 設定方法

Claude Codeの設定ファイル（`~/.claude/claude_desktop_config.json`）に以下を追加します。

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-server-playwright"]
    }
  }
}
```

設定後、Claude Codeを再起動すると有効になります。

### 検証プロセス

1. Marpプレビューサーバーを起動
2. Playwrightでブラウザを起動
3. 各スライドページを巡回
4. スクリーンショットを撮影
5. 視覚的な問題を検出・レポート

### 検出可能な問題

- **オーバーフロー**: テキストや画像がスライド領域を超えている
- **レイアウト崩れ**: 意図しない位置に要素が配置されている
- **表示の一貫性**: スライド間でフォントサイズや余白が不統一

# カスタムテーマの作成

組み込みテーマで物足りない場合、CSSでカスタムテーマを作成できます。

## テーマファイルの配置

```
themes/
└── corporate.css
```

## テーマの適用

```markdown
---
marp: true
theme: corporate
---
```

## カスタマイズ例

```css
/* @theme corporate */

section {
  background-color: #f5f5f5;
  color: #333;
}

section.lead {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

h1 {
  color: #2c3e50;
  border-bottom: 3px solid #3498db;
}

code {
  background-color: #ecf0f1;
  padding: 2px 6px;
  border-radius: 4px;
}
```

# Tips & トラブルシューティング

## 画像が表示されない

**原因**: 相対パスの指定ミス。

**解決策**: `slides/` からの相対パスで `./images/filename.png` と指定。

```markdown
![alt text](./images/diagram.png)
```

## レイアウトが崩れる

**原因**: 1スライドあたりの文字量が多すぎる。

**解決策**:

- 1スライド1メッセージを心がける
- `<!-- _class: lead -->` で中央揃えレイアウトを活用
- 箇条書きは5項目以内に

## 数式が表示されない

**原因**: Front Matterに `math: katex` がない。

**解決策**:

```markdown
---
marp: true
math: katex
---

インライン数式: $E = mc^2$

ブロック数式:
$$
\sum_{i=1}^{n} x_i = x_1 + x_2 + \cdots + x_n
$$
```

## PDF出力でフォントが崩れる

**原因**: システムフォントの問題。

**解決策**: Chromiumが使用するフォントを明示的に指定。

```css
section {
  font-family: 'Noto Sans JP', sans-serif;
}
```

# まとめ

## 実現できたこと

- **自然言語指示でのスライド作成**: コマンド不要で作成・レビュー・ビルド
- **Git完全対応のバージョン管理**: テキストベースで差分が追える
- **品質保証の自動化**: テキストチェック + 視覚的検証

## Claude Code Skills / Subagents 活用のポイント

| ポイント | 説明 |
|---------|------|
| **専門タスクの分離** | creator / reviewer / builder と役割を明確化 |
| **自然言語トリガー** | ユーザーの意図を汲み取って適切なエージェントを起動 |
| **Skillとの連携** | ドメイン知識を参照しながら高精度な出力を生成 |

## 今後の展開

- **CI/CD連携**: push時の自動ビルド・デプロイ
- **チームテンプレート**: 組織共通のデザインガイドラインをSkillに組み込み
- **他ドキュメントへの応用**: 同様の仕組みを技術文書作成にも適用

# 参考資料

- [Marp 公式サイト](https://marp.app/)
- [Marp CLI](https://github.com/marp-team/marp-cli)
- [marp-slides リポジトリ](https://github.com/toku345/marp-slides)
- [Claude Code 公式ドキュメント](https://docs.anthropic.com/en/docs/claude-code)
- [Playwright MCP](https://github.com/anthropics/mcp-servers/tree/main/src/playwright)
