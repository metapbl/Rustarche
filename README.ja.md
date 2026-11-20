<p align="center">
  <strong>Language:</strong>
  <a href="README.md">English</a> |
  <a href="README.ko.md">한국어</a> |
  <a href="README.zh-CN.md">简体中文</a> |
  <a href="README.ja.md">日本語</a> |
  <a href="README.es.md">Español</a> |
  <a href="README.ru.md">Русский</a>
</p>

<p align="center"><sub>この文書は英語版を AI で翻訳したものです。内容が異なる場合は英語版に従ってください。</sub></p>

# Rustarche

<p align="center">
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue?style=flat-square"></a>
  <a href="#インストール"><img alt="Agent Skill for Claude Code and Codex" src="https://img.shields.io/badge/Agent_Skill-Claude_Code_%7C_Codex-D97757?style=flat-square"></a>
  <a href="#必要環境"><img alt="Rust 1.97 or later" src="https://img.shields.io/badge/Rust-1.97%2B-B7410E?style=flat-square&logo=rust&logoColor=white"></a>
  <a href="#metarche-と一緒に使う"><img alt="Language skill for Metarche" src="https://img.shields.io/badge/Metarche-language_skill-555555?style=flat-square"></a>
</p>

Rust + archē：Rust 用の Cargo テンプレート、lint 設定、チェックコマンド、AGENTS.md のルールを提供するエージェントスキル。

> [!NOTE]
> スキルの指示文は英語で書かれています。このスキルがプロジェクトに追加するルールやメモは、あなたの言語で作成します。

## できること
- **new**：テンプレートから、lint・ツールチェーン固定・開発プロファイル入りの Cargo ワークスペースを作ります。
- **adopt**：既存の Rust コードに同じ設定と AGENTS.md のルールを加えます。既存のファイルは上書きしません。
- **refactor**：まず現在の動作を固定してから構造を変えます。他の言語のコードを Rust に移すことも扱います。
- AGENTS.md を短く保ちます。ルールの大半は Clippy と Cargo の設定が強制し、モデルが知らない落とし穴だけを文章で残します。
- async、unsafe、FFI、性能に関するメモは、そのコードを扱うときだけ読み込みます。

## 必要環境
- Rust 1.97 以降（`build.warnings`）。Windows（MSVC）の Rust 1.100.0 で確認済みです。
- Agent Skills と AGENTS.md を読むコーディングエージェント：[Claude Code](https://code.claude.com/docs)（テスト済み）または [Codex](https://learn.chatgpt.com/docs/build-skills)。
- git（推奨）

## インストール
Windows（PowerShell 7）：
~~~
git clone https://github.com/metapbl/Rustarche.git "$env:USERPROFILE\My Projects\Rustarche"
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills" | Out-Null
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\rustarche" -Target "$env:USERPROFILE\My Projects\Rustarche\skill"
~~~
macOS / Linux：
~~~
git clone https://github.com/metapbl/Rustarche.git ~/Rustarche
mkdir -p ~/.claude/skills && ln -s ~/Rustarche/skill ~/.claude/skills/rustarche
~~~
Codex（同じフォルダーを `~/.agents/skills` の下にリンク）、Windows：
~~~
New-Item -ItemType Directory -Force "$env:USERPROFILE\.agents\skills" | Out-Null
New-Item -ItemType Junction -Path "$env:USERPROFILE\.agents\skills\rustarche" -Target "$env:USERPROFILE\My Projects\Rustarche\skill"
~~~
macOS / Linux:
~~~
mkdir -p ~/.agents/skills && ln -s ~/Rustarche/skill ~/.agents/skills/rustarche
~~~
Claude Code または Codex の `/skills` に `rustarche` が表示されれば完了です。更新はリポジトリのフォルダーで `git pull` を実行します。

## 使い方
| コマンド | いつ使うか |
|---|---|
| `/rustarche` | 引数なし：`Cargo.toml` がなければ `new`、あれば `adopt` |
| `/rustarche new` | 新しい Rust ワークスペースを作る |
| `/rustarche adopt` | 既存の Rust コードに lint・ツールチェーン設定と AGENTS.md のルールを加える |
| `/rustarche refactor` | Rust コードのリファクタリング、または他の言語のコードを Rust に移す |

Rust プロジェクトに関する依頼なら、エージェントが自分でこのスキルを読み込むこともあります。Codex では `$rustarche` で呼び出します（例：`$rustarche new`）。

スキル全体で使うチェック：
~~~
cargo fmt --all --check
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
cargo test --workspace
~~~

## Metarche と一緒に使う
[Metarche](https://github.com/metapbl/Metarche) のプロジェクトでは Metarche が手順を進め、Rust の値は `skill/references/metarche.md` から読み込みます。質疑応答の質問、ファイル単位のチェック（`rustfmt --check --edition 2024`）、ターン終了時のチェック（`build.warnings` を有効にした Clippy）、AGENTS.md に入れる行、計画の最初のタスクがここにあります。`/rustarche` を別に呼ぶ必要はありません。

## 構成
~~~
skill/
├─ SKILL.md              入口：バージョン確認、モード判定、手順
├─ references/
│  ├─ rules.md           AGENTS.md に入れるコマンドとルール
│  ├─ metarche.md        Metarche プロジェクトに渡す値
│  ├─ refactor.md        リファクタリングと他言語からの移行
│  ├─ async.md           async を導入するかどうか、導入したときのルール
│  └─ unsafe-perf.md     unsafe、FFI、性能
└─ templates/            Cargo.toml.workspace、rust-toolchain.toml、clippy.toml、gitignore
~~~

## クレジット
このスキルが参考にした資料は [`NOTICE`](NOTICE) にあります。

## ライセンス
MIT
