# qiita-contents

[Qiita CLI](https://github.com/increments/qiita-cli)を使用したQiita記事管理リポジトリ。

## セットアップ

```bash
npm install
```

## 使い方

### 記事の新規作成

```bash
npx qiita new <basename>
```

生成されたファイルの `private: false` を `private: true` に変更してから執筆を開始してください。

### ローカルプレビュー

```bash
npx qiita preview
```

http://localhost:8888 でプレビューを確認できます。

### 校正

```bash
# すべてのLintを実行
npm run lint

# 自動修正
npm run lint:fix
```

## ディレクトリ構成

```
public/
├── .remote/    # Qiita CLIが管理（編集禁止）
└── *.md        # 記事ファイル
```

## 公開

mainブランチへのpushでGitHub Actionsにより自動公開されます。

### QIITA_TOKENの設定

1. [Qiitaの設定画面](https://qiita.com/settings/tokens/new)でアクセストークンを発行（`read_qiita`と`write_qiita`スコープが必要）
2. GitHubリポジトリのSettings → Secrets and variables → Actionsを開く
3. 「New repository secret」をクリック
4. Name: `QIITA_TOKEN`、Secret: 発行したトークンを入力して保存

## Lint ルール

| ツール | 設定ファイル | 用途 |
|--------|-------------|------|
| textlint | `.textlintrc.json` | 日本語校正 |
| markdownlint | `.markdownlint-cli2.jsonc` | Markdown構文チェック |
| markdown-link-check | - | リンク切れ検出 |

## Claude Code

[Claude Code](https://claude.ai/code)向けの専用コマンドとスキルが定義されている。

### スラッシュコマンド

| コマンド | 説明 |
|---------|------|
| `/qiita-new <basename>` | 新規記事作成（自動で`private: true`に設定） |
| `/qiita-preview` | ローカルプレビューサーバー起動 |
| `/qiita-lint [file]` | textlintによる日本語校正 |
| `/qiita-toggle-private <file>` | 公開/限定共有の切り替え |

### スキル

| スキル | 説明 |
|--------|------|
| `qiita-guidelines` | 記事作成時にQiitaガイドラインを参照 |

詳細は[CLAUDE.md](./CLAUDE.md)を参照。

## License

記事の著作権は著者に帰属します。
