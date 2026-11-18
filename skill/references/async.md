# async

## Whether to use it
- The default is sync. CLI, desktop and batch tools are written sync.
- Bring in tokio only when concurrent IO is central (many connections, a server) or a library you must use is async-only.
- Do not use it for: CPU-parallel computation (rayon), file-heavy work (no OS async file API, so no gain), a single request (the blocking version is simpler).
- If a sync program needs a little async, use a `current_thread` runtime with `block_on`, or run the runtime on its own thread and talk to it through bounded channels.

## Rules when you do
| Rule | Why |
|---|---|
| Do not block for long (over 10–100 µs) between `.await` points. No `std::thread::sleep`. Sync IO goes to `spawn_blocking`, CPU work to rayon, endless loops to a dedicated thread | the runtime thread stalls |
| Default to `std::sync::Mutex`. Never hold a guard across `.await` | reaching for `tokio::sync::Mutex` by default is a common mistake (clippy `await_holding_lock` catches the hold) |
| Channels are bounded. No cycles between bounded channels | unbounded memory growth, deadlock |
| Inside `select!`, `read_exact`, `write_all` and `Mutex::lock` are not cancellation-safe | data is lost when cancelled midway |
| Track spawned tasks with `JoinSet` or `TaskTracker` + `CancellationToken` | tasks leak at shutdown |
| `async fn` in a public trait cannot carry a `Send` bound. If needed, `trait_variant::make`; if `dyn` is needed, `async_trait` | it becomes unusable on a multi-threaded runtime |
| Libraries depend only on `tracing` and never set a global subscriber. In async code use `#[instrument]` or `.instrument()` | a `Span::enter` guard held across `.await` mixes up spans |
