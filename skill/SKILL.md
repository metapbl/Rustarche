---
name: rustarche
description: "Rust-only values for starting a Rust project (new), putting settings and rules on existing Rust code (adopt), refactoring Rust code, or porting code from Python, C++ or another language to Rust (refactor). Gives Cargo workspace, lint and toolchain templates, Rust rules for AGENTS.md and check commands. In a metarche project it leaves the workflow to metarche and supplies only the values. Use when starting, adopting or refactoring a Rust (Cargo) project, or porting code to Rust."
argument-hint: "[new|adopt|refactor]"
compatibility: "Works with any agent that reads Agent Skills and AGENTS.md (checked with Claude Code). Needs a Rust toolchain; checked with Rust 1.100.0."
---

# Rustarche — Rust + archē

This skill does not run a workflow (interview, approvals, records). It supplies only the values and rules Rust needs.
The basis is the research report listed in the repository's `NOTICE`. Most rules are enforced by lints and Cargo settings; only traps the model does not know and judgment rules are written as text.

## Language
These instructions are in English. Speak to the user, and write what you add to the project (AGENTS.md lines, REFACTOR.md, comments in copied templates when you adapt them), in the user's language: the language of the global instructions (`~/.claude/CLAUDE.md` for Claude Code, `~/.codex/AGENTS.md` for Codex), otherwise the language the user writes in. Commands, code and lint names stay as they are.

## Baseline version
- Checked against **Rust 1.100.0 (2026-11)**. At the start, run `rustc --version` and say so if it differs.
  - Below 1.97 there is no `build.warnings`. Block warnings with `cargo clippy ... -- -D warnings` instead.
  - If newer, say that changes since then are not reflected.
- Check crate APIs on docs.rs or with `cargo doc --no-deps`, not from memory (measurements show model accuracy drops sharply on newer APIs).

## Start: detect
1. If the first line of the root `AGENTS.md` (or, in older metarche projects, `CLAUDE.md`) starts with `<!-- metarche`, this is a **metarche project**. metarche installs hooks, keeps state and asks for approvals. Supply only the values in `references/metarche.md` when metarche asks for them. Do not use the steps below.
2. Otherwise follow the argument, or pick the mode from the folder: no `Cargo.toml` means new, one means adopt. A request to refactor or port means refactor.
3. Report the result and what you will do in one line, and get confirmation.

## new / adopt (when it is not a metarche project)
1. Show `templates/` and get approval for what to create. Do not overwrite existing files; show the difference.
   - `Cargo.toml.workspace` → root `Cargo.toml` (virtual manifest with lints and profiles)
   - `rust-toolchain.toml`, `clippy.toml`; merge `gitignore` into `.gitignore`
2. Create crates with `cargo new --lib crates/<name>` (or `--bin`). Inside the workspace, edition and rust-version inheritance and `[lints] workspace = true` are added automatically.
   - The sample function `cargo new` writes fails the pedantic lint `must_use_candidate` right away. Delete or fix it.
3. Copy the "Commands" and "Rules" sections of `references/rules.md` into the root `AGENTS.md`, in the user's language. If an AGENTS.md exists, leave out lines it already covers. For Claude Code, a `CLAUDE.md` containing only `@AGENTS.md` makes it read the same file.
4. Check: all three commands exit 0.
   ~~~
   cargo fmt --all --check
   cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
   cargo test --workspace
   ~~~
5. This skill has no per-file or end-of-turn check hooks. If metarche is installed, propose using its check and finish hooks and agent adapters (its `references/agents.md`) with the commands from `references/metarche.md` §2. Without hooks, run those checks yourself before calling a task done.

## refactor
Read and follow `references/refactor.md`. Outside a metarche project, keep progress in a root `REFACTOR.md` (behavior to pin, steps, what is left). In AGENTS.md write only its path, not an `@` import.

## Files to read only when needed
| File | When |
|---|---|
| `references/rules.md` | creating or changing AGENTS.md |
| `references/metarche.md` | metarche's discover, docs and plan stages |
| `references/refactor.md` | refactoring, porting from another language |
| `references/async.md` | deciding whether to use async, or changing async code |
| `references/unsafe-perf.md` | unsafe, FFI, performance work |
