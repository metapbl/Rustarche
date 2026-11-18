# async

## 들일지 정하기
- 기본은 sync다. CLI·데스크톱·배치 도구는 sync로 만든다.
- 동시 IO가 핵심이거나(많은 연결, 서버) 꼭 필요한 라이브러리가 async 전용일 때만 tokio를 들인다.
- 쓰지 않을 경우: CPU 병렬 계산(rayon), 파일을 많이 읽는 작업(OS 비동기 파일 API가 없어 이점이 없음), 요청 한 번(blocking 버전이 단순).
- sync 프로그램 안에 async가 조금 필요하면 `current_thread` 런타임과 `block_on`, 또는 런타임을 별도 스레드에 두고 bounded 채널로 주고받는다.

## 쓸 때의 규칙
| 규칙 | 이유 |
|---|---|
| `.await` 사이에 오래(10~100µs 넘게) 블로킹하지 않는다. `std::thread::sleep` 금지. sync IO는 `spawn_blocking`, CPU 작업은 rayon, 끝없이 도는 작업은 전용 스레드 | 런타임 스레드가 멈춘다 |
| 락은 기본 `std::sync::Mutex`. 가드를 쥔 채 `.await`하지 않는다 | 무조건 `tokio::sync::Mutex`는 흔한 실수 (clippy `await_holding_lock`이 잡음) |
| 채널은 bounded. bounded 채널끼리 순환을 만들지 않는다 | 메모리 무한 증가, 교착 |
| `select!` 안에서 `read_exact`, `write_all`, `Mutex::lock`은 취소에 안전하지 않다 | 중간에 취소되면 데이터가 사라진다 |
| spawn한 작업은 `JoinSet`이나 `TaskTracker` + `CancellationToken`으로 추적한다 | 종료 때 새는 작업 |
| 공개 트레이트의 `async fn`은 `Send` bound를 못 붙인다. 필요하면 `trait_variant::make`, `dyn`이 필요하면 `async_trait` | 멀티스레드 런타임에서 쓸 수 없게 된다 |
| 라이브러리는 `tracing`만 의존하고 전역 구독자를 설정하지 않는다. async에서는 `#[instrument]`나 `.instrument()` | `Span::enter` 가드가 `.await`를 넘으면 span이 섞인다 |
