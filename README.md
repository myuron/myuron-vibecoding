# myuron-vibecoding

Claude Code を使った vibe coding のテンプレートリポジトリです。
コードインテリジェンス・ライブラリ検索・PR レビューなどの MCP を事前設定済みで、すぐに開発を始められます。

---

## 利用している MCP

| MCP | 用途 | 設定ファイル |
|-----|------|-------------|
| [Serena](https://github.com/oraios/serena) | コード検索・シンボル解析・コードインテリジェンス | `.mcp.json` |
| [Context7](https://github.com/upstash/context7) | ライブラリ・フレームワークの最新ドキュメント取得 | `.mcp.json` |

### Serena MCP
コードベースのシンボル検索・参照解析・リネームなど IDE 的なコード操作を提供します。
`CLAUDE.md` に「コードを検索する場合は Serena MCP を使う」と指示を記載し、Claude が自動的に使用します。

### Context7 MCP
ライブラリやツールの公式ドキュメント・コード例をリアルタイムで取得します。
`CLAUDE.md` に「ライブラリやツールを使用する場合は Context7 MCP を使う」と指示を記載し、Claude が自動的に使用します。

---

## ディレクトリ構成

```
.
├── flake.nix                      # Nix flake による開発環境定義
├── flake.lock                     # Nix flake ロックファイル
├── .envrc                         # direnv 設定 (use flake)
├── CLAUDE.md                      # Claude Code への指示
├── README.md                      # このファイル
├── .mcp.json                      # MCP サーバー設定
├── .coderabbit.yaml               # CodeRabbit 設定（日本語レビュー）
├── .gitignore
├── .claude/
│   ├── settings.local.json        # Claude Code ローカル設定
│   └── commands/                  # カスタムスラッシュコマンド
│       ├── setup-serena.md        # /setup-serena
│       ├── review-coderabbit.md   # /review-coderabbit
│       └── review-codex.md        # /review-codex
└── .serena/
    ├── project.yml                # Serena プロジェクト設定
    └── memories/                  # Serena メモリ（会話間で引き継ぐ情報）
```

---

## 開発環境

[Nix](https://nixos.org/) + [direnv](https://direnv.net/) で再現可能な開発環境を提供します。

`flake.nix` に定義されているパッケージ:
- `uv` + `python3` — Python パッケージ管理・実行
- `pnpm` + `nodejs` — Node.js パッケージ管理（Context7 MCP の実行に使用）

---

## 初期セットアップ

### 1. 前提条件のインストール

```bash
# Nix のインストール（未インストールの場合）
curl --proto '=https' --tlsv1.2 -sSf -L https://install.determinate.systems/nix | sh -s -- install

# direnv のインストール（未インストールの場合）
# macOS
brew install direnv

# Ubuntu/Debian
sudo apt install direnv
```

シェルの設定ファイル（`~/.zshrc` や `~/.bashrc`）に以下を追加:
```bash
eval "$(direnv hook zsh)"   # zsh の場合
eval "$(direnv hook bash)"  # bash の場合
```

### 2. リポジトリのクローンと環境の有効化

```bash
git clone https://github.com/myuron/myuron-vibecoding.git
cd myuron-vibecoding

# direnv を許可（初回のみ）
direnv allow
```

direnv が自動的に Nix devShell に入り、必要なツールが利用可能になります。

### 3. Claude Code のインストール

```bash
npm install -g @anthropic-ai/claude-code
```

### 4. Serena MCP のセットアップ

Claude Code を起動後、以下のカスタムコマンドを実行してプロジェクトを Serena に読み込ませます:

```
/setup-serena
```

---

## カスタムスラッシュコマンド

| コマンド | 説明 |
|---------|------|
| `/setup-serena` | Serena MCP にプロジェクトを読み込ませる初期化処理 |
| `/review-coderabbit <PR番号>` | CodeRabbit のレビュー結果を取得し、修正計画を立てる |
| `/review-codex <PR番号>` | Codex にコードレビューを依頼し、修正計画を立てる |

---

## PR レビュー（CodeRabbit）

`.coderabbit.yaml` で日本語（`ja-JP`）レビューを設定済みです。
GitHub Actions と連携することで、PR 作成時に自動的に日本語でレビューコメントが付きます。

---

## テンプレートとして使う

このリポジトリを新しいプロジェクトのベースにする場合:

1. GitHub の「Use this template」からリポジトリを作成
2. クローンして `direnv allow` を実行
3. `flake.nix` に必要なパッケージを追加
4. `CLAUDE.md` にプロジェクト固有の指示を追記
5. `/setup-serena` で Serena を初期化
