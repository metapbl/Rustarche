<p align="center">
  <strong>Language:</strong>
  <a href="README.md">English</a> |
  <a href="README.ko.md">한국어</a> |
  <a href="README.zh-CN.md">简体中文</a> |
  <a href="README.ja.md">日本語</a> |
  <a href="README.es.md">Español</a> |
  <a href="README.ru.md">Русский</a>
</p>

# Rustarche

<p align="center">
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue?style=flat-square"></a>
  <a href="#install"><img alt="Agent Skill for Claude Code and Codex" src="https://img.shields.io/badge/Agent_Skill-Claude_Code_%7C_Codex-D97757?style=flat-square"></a>
  <a href="#requirements"><img alt="Rust 1.97 or later" src="https://img.shields.io/badge/Rust-1.97%2B-B7410E?style=flat-square&logo=rust&logoColor=white"></a>
  <a href="#use-with-metarche"><img alt="Language skill for Metarche" src="https://img.shields.io/badge/Metarche-language_skill-555555?style=flat-square"></a>
</p>

Rust + archē: an agent skill with Cargo templates, lint settings, check commands and AGENTS.md rules for Rust.

> [!NOTE]
> The skill's instructions are in English. It writes the rules and notes it adds to your project in your language.

## What it does
- **new**: sets up a Cargo workspace with lints, toolchain pin and dev profiles from the templates.
- **adopt**: adds the same settings and AGENTS.md rules to existing Rust code without overwriting files.
- **refactor**: locks current behavior first, then changes structure; also covers porting code from other languages to Rust.
- Keeps AGENTS.md short: most rules are enforced by Clippy and Cargo settings, and only traps the model does not know are written as text.
- Loads extra notes on async, unsafe, FFI and performance only when that code is in play.

## Requirements
- Rust 1.97 or later (`build.warnings`). Checked with Rust 1.100.0 on Windows (MSVC).
- A coding agent that reads Agent Skills and AGENTS.md: [Claude Code](https://code.claude.com/docs) (tested) or [Codex](https://learn.chatgpt.com/docs/build-skills).
- git (recommended)

## Install
Windows (PowerShell 7):
~~~
git clone https://github.com/metapbl/Rustarche.git "$env:USERPROFILE\My Projects\Rustarche"
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills" | Out-Null
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\rustarche" -Target "$env:USERPROFILE\My Projects\Rustarche\skill"
~~~
macOS / Linux:
~~~
git clone https://github.com/metapbl/Rustarche.git ~/Rustarche
mkdir -p ~/.claude/skills && ln -s ~/Rustarche/skill ~/.claude/skills/rustarche
~~~
Codex (the same folder under `~/.agents/skills`), Windows:
~~~
New-Item -ItemType Directory -Force "$env:USERPROFILE\.agents\skills" | Out-Null
New-Item -ItemType Junction -Path "$env:USERPROFILE\.agents\skills\rustarche" -Target "$env:USERPROFILE\My Projects\Rustarche\skill"
~~~
macOS / Linux:
~~~
mkdir -p ~/.agents/skills && ln -s ~/Rustarche/skill ~/.agents/skills/rustarche
~~~
You are done when `rustarche` appears in `/skills` in Claude Code or Codex. To update, run `git pull` in the repository folder.

## Quick start
In an empty folder, start your agent and call the skill (`$rustarche new` in Codex):
~~~
> /rustarche new
~~~
It shows the templates and asks before creating anything. Once you approve, it creates the workspace and runs the three checks. A fresh workspace passes all of them:
~~~
cargo fmt --all --check                                               # prints nothing, exit 0
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'  # ends with: Finished `dev` profile
cargo test --workspace                                                # test result: ok.
~~~

## Usage
| Command | When |
|---|---|
| `/rustarche` | No argument: `new` if there is no `Cargo.toml`, otherwise `adopt` |
| `/rustarche new` | Create a new Rust workspace |
| `/rustarche adopt` | Add lint and toolchain settings and AGENTS.md rules to existing Rust code |
| `/rustarche refactor` | Refactor Rust code, or port code from another language to Rust |

The agent may also load the skill on its own when a request is about a Rust project. In Codex, call it as `$rustarche` (e.g. `$rustarche new`).

Checks used across the skill:
~~~
cargo fmt --all --check
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
cargo test --workspace
~~~

## Use with Metarche
In a [Metarche](https://github.com/metapbl/Metarche) project, Metarche runs the workflow and reads `skill/references/metarche.md` for the Rust values: interview questions, the per-file check (`rustfmt --check --edition 2024`), the end-of-turn check (Clippy with `build.warnings`), AGENTS.md lines and the first plan task. You do not need to call `/rustarche` separately.

## Structure
~~~
skill/
├─ SKILL.md              entry point: version check, mode detection, steps
├─ references/
│  ├─ rules.md           commands and rules to put into AGENTS.md
│  ├─ metarche.md        values for Metarche projects
│  ├─ refactor.md        refactoring and porting
│  ├─ async.md           when to use async, and rules when you do
│  └─ unsafe-perf.md     unsafe, FFI and performance
└─ templates/            Cargo.toml.workspace, rust-toolchain.toml, clippy.toml, gitignore
~~~

## Project status
- Checked with Rust 1.100.0 on Windows (MSVC) in Claude Code: a new workspace from the templates formats, lints and tests cleanly.
- Not yet tried in Codex or on a large codebase.

## Help and contributing
- Questions and bug reports: [GitHub Issues](https://github.com/metapbl/Rustarche/issues).
- Pull requests: open an issue first to agree on the change. Keep the skill text in English, and keep a new workspace from the templates passing the three checks in the quick start.

## Security
Report vulnerabilities privately through [GitHub private vulnerability reporting](https://github.com/metapbl/Rustarche/security/advisories/new), not in a public issue.

## Credits
Sources this skill draws on are listed in [`NOTICE`](NOTICE).

## License
MIT
