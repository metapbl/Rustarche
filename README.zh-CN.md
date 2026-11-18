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
  <a href="#安装"><img alt="Claude Code skill" src="https://img.shields.io/badge/Claude_Code-skill-D97757?style=flat-square&logo=claude&logoColor=white"></a>
  <a href="#环境要求"><img alt="Rust 1.97 or later" src="https://img.shields.io/badge/Rust-1.97%2B-B7410E?style=flat-square&logo=rust&logoColor=white"></a>
  <a href="#与-metarche-一起使用"><img alt="Language skill for Metarche" src="https://img.shields.io/badge/Metarche-language_skill-555555?style=flat-square"></a>
</p>

Rust + archē：为 Rust 提供 Cargo 模板、lint 设置、检查命令和 CLAUDE.md 规则的 Claude Code 技能。

> [!NOTE]
> 本技能的指令以英语编写。它添加到项目中的规则和说明会使用你的语言。

## 功能
- **new**：用模板创建带有 lint、工具链固定和开发配置的 Cargo 工作区。
- **adopt**：为现有 Rust 代码加上同样的设置和 CLAUDE.md 规则，不覆盖已有文件。
- **refactor**：先固定当前行为，再改结构；也包括把其他语言的代码迁移到 Rust。
- 保持 CLAUDE.md 简短：大部分规则由 Clippy 和 Cargo 设置强制执行，只把模型不知道的陷阱写成文字。
- 只有在处理相关代码时，才读取关于 async、unsafe、FFI 和性能的说明。

## 环境要求
- Rust 1.97 或更高版本（`build.warnings`）。已在 Windows（MSVC）上用 Rust 1.100.0 验证。
- [Claude Code](https://code.claude.com/docs)
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
在 Claude Code 的 `/skills` 中看到 `rustarche` 即安装完成。更新时在仓库文件夹中运行 `git pull`。

## 使用
| 命令 | 何时使用 |
|---|---|
| `/rustarche` | 不带参数：没有 `Cargo.toml` 时为 `new`，否则为 `adopt` |
| `/rustarche new` | 创建新的 Rust 工作区 |
| `/rustarche adopt` | 为现有 Rust 代码添加 lint、工具链设置和 CLAUDE.md 规则 |
| `/rustarche refactor` | 重构 Rust 代码，或把其他语言的代码迁移到 Rust |

当请求与 Rust 项目相关时，Claude 也可能自动加载本技能。

本技能统一使用的检查：
~~~
cargo fmt --all --check
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
cargo test --workspace
~~~

## 与 Metarche 一起使用
在 [Metarche](https://github.com/metapbl/Metarche) 项目中，由 Metarche 执行流程，并从 `skill/references/metarche.md` 读取 Rust 相关的值：问答问题、按文件检查（`rustfmt --check --edition 2024`）、回合结束检查（启用 `build.warnings` 的 Clippy）、要写入 CLAUDE.md 的内容和计划的第一个任务。无需另外调用 `/rustarche`。

## 结构
~~~
skill/
├─ SKILL.md              入口：版本检查、模式判断、步骤
├─ references/
│  ├─ rules.md           写入 CLAUDE.md 的命令和规则
│  ├─ metarche.md        提供给 Metarche 项目的值
│  ├─ refactor.md        重构与跨语言迁移
│  ├─ async.md           是否采用 async，以及采用后的规则
│  └─ unsafe-perf.md     unsafe、FFI 与性能
└─ templates/            Cargo.toml.workspace、rust-toolchain.toml、clippy.toml、gitignore
~~~

## 致谢
本技能参考的资料列在 [`NOTICE`](NOTICE) 中。

## 许可证
MIT
