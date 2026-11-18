# unsafe, FFI and performance

## unsafe
- The only good reasons are a new safe abstraction, performance proven by a benchmark, and FFI. Never to save code or to get around `Send` or lifetimes. Do not mark a function `unsafe` just because it "looks dangerous".
- Confine unsafe to one module and use privacy as the boundary. That module's safe functions uphold the invariants.
- Above every block, write a `// SAFETY:` comment with the condition that holds. In that crate lift `unsafe_code = "forbid"` and keep the `undocumented_unsafe_blocks` lint on (it is in the workspace template).
- Edition 2024: `unsafe {}` blocks per operation even inside an `unsafe fn`, no references to `static mut`, `env::set_var` is unsafe, `#[unsafe(no_mangle)]`.
- Find UB with Miri (`cargo miri test`; on Windows, `--target x86_64-unknown-linux-gnu`). Finding nothing does not prove soundness.

## Performance
- Measure first. Fix only the hot spots a profile shows (samply on Windows). The biggest gains come from algorithm and data structure changes.
- Measure release builds. Build in release mode only when asked (rules.md).
- Benchmark every change to LTO, `codegen-units = 1`, `panic = "abort"`, `#[inline]` or the allocator. `#[inline]` can make things slower.
- Free habits: `with_capacity` when the size is known, `BufReader`/`BufWriter` for files, return `impl Iterator` instead of `collect` when the caller only iterates.
- Clones in cold code are fine. Remove only the clones a profile shows as hot.
