# Rust values for metarche projects

Each metarche stage reads this file. The workflow, records and approvals follow metarche; only the values come from here.

## §1 discover — questions to add to "4 Approach"
1. Output: binary / library / both — recommended: start with one binary (a library when another crate uses it)
2. Is async needed: is concurrent IO (many connections, a web server) central? — recommended: if not, sync. If so, `references/async.md`
3. Edition and minimum version: edition 2024, `rust-version` = the version in use — recommended: as is (the simplest policy is always latest)
4. Is unsafe or FFI needed? — recommended: no (`unsafe_code = "forbid"`). If so, `references/unsafe-perf.md`
5. Published as a public library? — recommended: no. If yes, run `cargo semver-checks` on public API changes
6. Are several sessions run in parallel? — recommend the default of one `target/` per worktree. If disk is a concern, a person adds `build.build-dir = "{cargo-cache-home}/build/{workspace-path-hash}"` to the user cargo config (checked on 1.100). Never run `cargo clean` in a shared directory.

## §2 discover — values for "5 Verification and operations"
| Item | Value |
|---|---|
| Full test | `cargo test --workspace` (+ `cargo test --doc` for a library). Set the baseline on incremental builds, excluding the first build — recommended: 120 seconds |
| Per-file checks | `'.rs' = @('rustfmt --check --edition 2024')` — always give the edition; without it 2024 syntax such as let chains is an error |
| End-of-turn checks (project-wide) | `'cargo clippy --workspace --all-targets --config build.warnings="deny"'` — in the finish.ps1 list, in this form without outer single quotes. Wrap it as `'build.warnings="deny"'` only when typing it in a shell |
| Dangerous commands (settings.json ask) | `Bash(cargo update*)`, `Bash(cargo clean*)`, `Bash(cargo build --release*)`, `Bash(cargo install*)` and the same `PowerShell(...)` rules |

- `cargo check` and `cargo clippy` cannot take a file path, so they do not belong in the per-file check (check hook).
- Block warnings with `build.warnings` instead of `RUSTFLAGS=-Dwarnings` or `-- -D warnings`. It does not invalidate the build cache (1.97 or later).

## §3 docs — CLAUDE.md
- Copy the matching lines of `references/rules.md` into the "Commands" and "Rules for this project" sections, in the project's document language. If it exceeds 60 lines, move rule lines to REFERENCE first.
- Trap log candidates for REFERENCE (record when first hit):
  - The sample function `cargo new` writes fails the pedantic lints right away.
  - An exact version in `rust-toolchain.toml` downloads that toolchain separately (even when it equals stable).

## §4 plan — task 1 (skeleton)
- What: a workspace skeleton from `templates/` — root `Cargo.toml` (virtual manifest, `[workspace.lints]`, profiles), `rust-toolchain.toml`, `clippy.toml`, `.gitignore`, the first crate (`cargo new --bin crates/{name}`).
- Check: these three commands exit 0.
  ~~~
  cargo fmt --all --check
  cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
  cargo test --workspace
  ~~~
- For a refactoring or porting project, the task after the skeleton pins the current behavior (`references/refactor.md` §1).
