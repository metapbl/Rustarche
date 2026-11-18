# metarche 프로젝트에 줄 Rust 값

metarche의 각 단계가 이 파일을 읽는다. 절차·기록·승인은 metarche를 따르고, 여기서는 값만 가져간다.

## §1 discover — 4 방식에 더할 질문
1. 산출물: 바이너리 / 라이브러리 / 둘 다 — 추천: 바이너리 하나로 시작 (라이브러리는 다른 crate가 쓸 때)
2. async가 필요한가: 동시 IO(많은 연결, 웹 서버)가 핵심인가 — 추천: 아니면 sync. 필요하면 `references/async.md`
3. edition과 최소 버전: edition 2024, `rust-version`은 지금 쓰는 버전 — 추천: 그대로 (가장 단순한 정책은 항상 최신)
4. unsafe·FFI가 필요한가 — 추천: 없음 (`unsafe_code = "forbid"`). 필요하면 `references/unsafe-perf.md`
5. 공개 라이브러리로 배포하는가 — 추천: 아님. 배포하면 공개 API 변경 때 `cargo semver-checks`
6. 여러 세션을 동시에 돌리는가 — 각 worktree가 자기 `target/`를 쓰는 기본값을 추천한다. 디스크가 부담되면 사람이 사용자 cargo 설정에 `build.build-dir = "{cargo-cache-home}/build/{workspace-path-hash}"`를 넣는다 (1.100에서 동작 확인). 공유 디렉터리에서 `cargo clean`은 하지 않는다.

## §2 discover — 5 검증·운영의 값
| 항목 | 값 |
|---|---|
| 전체 테스트 | `cargo test --workspace` (+ 라이브러리면 `cargo test --doc`). 기준 시간은 첫 빌드를 뺀 증분 빌드 기준으로 정한다 — 추천: 120초 |
| 편집 때 자동 검사 (파일 단위) | `'.rs' = @('rustfmt --check --edition 2024')` — edition을 꼭 적는다. 없으면 let chains 같은 2024 문법을 오류로 본다 |
| 작업 끝 자동 검사 (프로젝트 단위) | `'cargo clippy --workspace --all-targets --config build.warnings="deny"'` — finish.ps1 표에는 바깥 작은따옴표 없이 이 형태로. 셸에서 직접 칠 때만 `'build.warnings="deny"'`로 감싼다 |
| 위험한 명령 (settings.json ask) | `Bash(cargo update*)`, `Bash(cargo clean*)`, `Bash(cargo build --release*)`, `Bash(cargo install*)`와 같은 `PowerShell(...)` 규칙 |

- `cargo check`·`cargo clippy`는 파일 경로를 받지 못하므로 편집 때 검사(check 훅)에 넣지 않는다.
- 경고 차단은 `RUSTFLAGS=-Dwarnings`나 `-- -D warnings` 대신 `build.warnings`를 쓴다. 빌드 캐시를 무효화하지 않는다 (1.97 이상).

## §3 docs — CLAUDE.md
- "명령" 절과 "이 프로젝트의 규칙" 절에 `references/rules.md`의 해당 줄을 옮긴다. 60줄을 넘으면 규칙 줄부터 REFERENCE로 내린다.
- REFERENCE 함정 대장 후보 (처음 겪으면 적는다):
  - `cargo new`가 만든 견본 함수는 pedantic lint에 바로 걸린다.
  - `rust-toolchain.toml`에 정확한 버전을 적으면 그 버전 툴체인을 따로 내려받는다 (stable과 같은 버전이어도).

## §4 plan — 계획 1번 작업 (뼈대)
- 무엇: `templates/`로 워크스페이스 뼈대 — 루트 `Cargo.toml`(가상 매니페스트, `[workspace.lints]`, 프로필), `rust-toolchain.toml`, `clippy.toml`, `.gitignore`, 첫 crate(`cargo new --bin crates/{이름}`).
- 확인: 아래 세 명령이 exit 0.
  ~~~
  cargo fmt --all --check
  cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
  cargo test --workspace
  ~~~
- 리팩토링·이전 프로젝트면 뼈대 다음 작업은 동작 고정이다 (`references/refactor.md` §1).
