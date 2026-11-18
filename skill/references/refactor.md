# Refactoring and porting from another language

The general rule of keeping structural and behavioral changes apart lives in metarche (plan.md §2). This file adds only what Rust needs.

## §1 Pin behavior before touching it
- For code without tests, pin the current behavior first with snapshots (insta) or golden files, in a commit of its own.
- If a snapshot changes during a pure refactor, do not accept it. Treat it as a regression, stop and report.
- Do not edit snapshot files by hand. Regenerate them (`cargo insta review`) and show the diff.
- When porting from another language, keep a test that feeds the same input to the old and new implementations and compares the output. Move over a little at a time, always in a shippable state.

## §2 While porting
- Do not turn places where the original code had an error path into `unwrap`, `expect` or assert. Port them to `Result` (this is where real ports produced the most bugs).
- While porting, the overall structure may stay as it was to keep before and after comparable. But replace the other language's idioms (null-check helpers, inheritance hierarchies, exception flow) with Rust's (`Option`, enums and traits, `Result`).
- Turn external input into validated types at the boundary. Heavy techniques such as typestate only when asked.

## §3 Rust tools
- Match on your own crate's enums without `_` (lint `wildcard_enum_match_arm`). New variants then show up as compile errors where code must change.
- `#[non_exhaustive]` has no effect inside the crate that defines it.
- When changing a public library's API, run `cargo semver-checks`. Passing is necessary, not sufficient (it does not catch every violation).
- For a Python extension (PyO3), release the GIL during long-running Rust work. Check the current API name on docs.rs.
