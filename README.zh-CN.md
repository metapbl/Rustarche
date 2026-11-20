<p align="center">
  <strong>Language:</strong>
  <a href="README.md">English</a> |
  <a href="README.ko.md">한국어</a> |
  <a href="README.zh-CN.md">简体中文</a> |
  <a href="README.ja.md">日本語</a> |
  <a href="README.es.md">Español</a> |
  <a href="README.ru.md">Русский</a>
</p>

<p align="center"><sub>本文档由 AI 从英文版翻译而来。如有出入，以英文版为准。</sub></p>

# Rustarche

<p align="center">
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue?style=flat-square"></a>
  <a href="#安装"><img alt="Agent Skill for Claude Code and Codex" src="https://img.shields.io/badge/Agent_Skill-Claude_Code_%7C_Codex-D97757?style=flat-square"></a>
  <a href="#环境要求"><img alt="Rust 1.97 or later" src="https://img.shields.io/badge/Rust-1.97%2B-B7410E?style=flat-square&logo=rust&logoColor=white"></a>
  <a href="#与-metarche-一起使用"><img alt="Language skill for Metarche" src="https://img.shields.io/badge/Metarche-language_skill-555555?style=flat-square"></a>
</p>

Rust + archē：为 Rust 提供 Cargo 模板、lint 设置、检查命令和 AGENTS.md 规则的智能体技能。

> [!NOTE]
> 本技能的指令以英语编写。它添加到项目中的规则和说明会使用你的语言。

## 功能
- **new**：用模板创建带有 lint、工具链固定和开发配置的 Cargo 工作区。
- **adopt**：为现有 Rust 代码加上同样的设置和 AGENTS.md 规则，不覆盖已有文件。
- **refactor**：先固定当前行为，再改结构；也包括把其他语言的代码迁移到 Rust。
- 保持 AGENTS.md 简短：大部分规则由 Clippy 和 Cargo 设置强制执行，只把模型不知道的陷阱写成文字。
- 只有在处理相关代码时，才读取关于 async、unsafe、FFI 和性能的说明。

## 环境要求
- Rust 1.97 或更高版本（`build.warnings`）。已在 Windows（MSVC）上用 Rust 1.100.0 验证。
- 能读取 Agent Skills 和 AGENTS.md 的编程智能体：[Claude Code](https://code.claude.com/docs)（已测试）或 [Codex](https://learn.chatgpt.com/docs/build-skills)。
- git（推荐）

## 安装
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
Codex（把同一文件夹链接到 `~/.agents/skills` 下），Windows：
~~~
New-Item -ItemType Directory -Force "$env:USERPROFILE\.agents\skills" | Out-Null
New-Item -ItemType Junction -Path "$env:USERPROFILE\.agents\skills\rustarche" -Target "$env:USERPROFILE\My Projects\Rustarche\skill"
~~~
macOS / Linux:
~~~
mkdir -p ~/.agents/skills && ln -s ~/Rustarche/skill ~/.agents/skills/rustarche
~~~
在 Claude Code 或 Codex 的 `/skills` 中看到 `rustarche` 即安装完成。更新时在仓库文件夹中运行 `git pull`。

## 快速开始
在空文件夹中启动智能体并调用本技能（Codex 中为 `$rustarche new`）：
~~~
> /rustarche new
~~~
它会先展示模板并在创建任何文件前征求同意。批准后，它创建工作区并运行三项检查。新的工作区会全部通过：
~~~
cargo fmt --all --check                                               # prints nothing, exit 0
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'  # ends with: Finished `dev` profile
cargo test --workspace                                                # test result: ok.
~~~

## 使用
| 命令 | 何时使用 |
|---|---|
| `/rustarche` | 不带参数：没有 `Cargo.toml` 时为 `new`，否则为 `adopt` |
| `/rustarche new` | 创建新的 Rust 工作区 |
| `/rustarche adopt` | 为现有 Rust 代码添加 lint、工具链设置和 AGENTS.md 规则 |
| `/rustarche refactor` | 重构 Rust 代码，或把其他语言的代码迁移到 Rust |

当请求与 Rust 项目相关时，智能体也可能自动加载本技能。在 Codex 中用 `$rustarche` 调用（例如 `$rustarche new`）。

本技能统一使用的检查：
~~~
cargo fmt --all --check
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
cargo test --workspace
~~~

## 与 Metarche 一起使用
在 [Metarche](https://github.com/metapbl/Metarche) 项目中，由 Metarche 执行流程，并从 `skill/references/metarche.md` 读取 Rust 相关的值：问答问题、按文件检查（`rustfmt --check --edition 2024`）、回合结束检查（启用 `build.warnings` 的 Clippy）、要写入 AGENTS.md 的内容和计划的第一个任务。无需另外调用 `/rustarche`。

## 结构
~~~
skill/
├─ SKILL.md              入口：版本检查、模式判断、步骤
├─ references/
│  ├─ rules.md           写入 AGENTS.md 的命令和规则
│  ├─ metarche.md        提供给 Metarche 项目的值
│  ├─ refactor.md        重构与跨语言迁移
│  ├─ async.md           是否采用 async，以及采用后的规则
│  └─ unsafe-perf.md     unsafe、FFI 与性能
└─ templates/            Cargo.toml.workspace、rust-toolchain.toml、clippy.toml、gitignore
~~~

## 项目状态
- 已在 Windows（MSVC）上用 Rust 1.100.0 和 Claude Code 验证：由模板生成的新工作区能通过格式、lint 和测试检查。
- 尚未在 Codex 或大型代码库中试用。

## 帮助与贡献
- 提问和报告缺陷：[GitHub Issues](https://github.com/metapbl/Rustarche/issues)。
- Pull request：请先开 issue 讨论改动。技能文本保持英文，并确保由模板生成的新工作区通过快速开始中的三项检查。

## 安全
请通过 [GitHub 私密漏洞报告](https://github.com/metapbl/Rustarche/security/advisories/new) 报告漏洞，不要发在公开 issue 中。

## 致谢
本技能参考的资料列在 [`NOTICE`](NOTICE) 中。

## 许可证
MIT
