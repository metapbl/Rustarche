# Rust commands and rules for AGENTS.md

Leave out what lints, rustfmt and hooks already enforce. Copy only the lines below, in the user's language, and replace `{crate}` with the real name.
No capitalized emphasis (IMPORTANT, NEVER).

## Commands
- Quick check while editing: `cargo check -p {crate}`
- One test: `cargo test -p {crate} {part of the name}` · doc tests: `cargo test --doc`
- Done check: `cargo clippy --workspace --all-targets --config 'build.warnings="deny"'`
- Formatting: `cargo fmt --all` (do not change the config file)

## Rules
- Do not change `RUSTFLAGS`, features or profiles from the shell. Build in release mode only when asked.
- Say so before adding a dependency, running `cargo update` or changing features. Add with `cargo add`; change the lockfile only with `cargo update -p {crate} --precise {version}`.
- Check crate APIs on docs.rs or with `cargo doc --no-deps`. Do not trust remembered signatures.
- Do not assume a warning or a failing test was already there.
- Handle expected failures (IO, input, network) with `Result`. Binaries use `anyhow` + `.context()`; library boundaries use typed errors. Log errors only where they are handled.
- On a borrow error, change the structure first (clarify the owner, use indices, split borrows, `mem::take`). Clone only on purpose.
- Fix the cause instead of silencing a lint. If you must silence it, `#[expect(clippy::{lint}, reason = "...")]`. Delete dead code.
- Bring in async, unsafe, `#[inline]`, LTO or allocator changes only with evidence (a need for concurrent IO, a benchmark, a profile).
- Comments and docs describe only the final state. Do not leave re-exports at the old path of moved items.

## Left out (tools handle these)
Naming, formatting and import order (rustfmt), match without `_` (`wildcard_enum_match_arm`), no unwrap (`unwrap_used`), silencing lints without a reason (`allow_attributes_without_reason`), let chains and inline format args (clippy), `#![deny(warnings)]` (not in source; use `build.warnings`).
