# CLAUDE.md에 옮길 Rust 명령과 규칙

lint·rustfmt·훅이 강제하는 것은 넣지 않는다. 아래만 옮기고, `{crate}`는 실제 이름으로 바꾼다.
대문자 강조(IMPORTANT, NEVER)는 쓰지 않는다.

## 명령
- 편집 중 빠른 확인: `cargo check -p {crate}`
- 테스트 하나: `cargo test -p {crate} {이름 일부}` · 문서 테스트: `cargo test --doc`
- 완료 확인: `cargo clippy --workspace --all-targets --config 'build.warnings="deny"'`
- 서식: `cargo fmt --all` (설정 파일을 바꾸지 않는다)

## 규칙
- 셸에서 `RUSTFLAGS`, feature, 프로필을 바꾸지 않는다. release 빌드는 요청이 있을 때만 한다.
- 의존성 추가, `cargo update`, feature 변경은 먼저 말하고 한다. 추가는 `cargo add`, lockfile은 `cargo update -p {crate} --precise {버전}`으로만 바꾼다.
- crate API는 docs.rs나 `cargo doc --no-deps`로 확인한다. 기억한 시그니처를 믿지 않는다.
- 경고나 테스트 실패를 원래 있던 것으로 가정하지 않는다.
- 예상 가능한 실패(IO, 입력, 네트워크)는 `Result`로 처리한다. 바이너리는 `anyhow` + `.context()`, 라이브러리 경계는 타입 있는 에러. 로그는 에러를 처리하는 곳에서만 남긴다.
- borrow 오류가 나면 구조(소유자 정리, 인덱스, borrow 나누기, `mem::take`)부터 바꾼다. clone은 의도가 있을 때만.
- lint를 끄지 말고 원인을 고친다. 꼭 꺼야 하면 `#[expect(clippy::{lint}, reason = "...")]`. 죽은 코드는 지운다.
- async, unsafe, `#[inline]`, LTO·allocator 변경은 근거(동시 IO 요구, 벤치마크, 프로파일)가 있을 때만 들인다.
- 주석과 문서에는 최종 상태만 쓴다. 옮긴 항목의 옛 경로 re-export를 남기지 않는다.

## 넣지 않는 것 (도구가 맡음)
명명·서식·import 정렬(rustfmt), `_` 없는 match(`wildcard_enum_match_arm`), unwrap 금지(`unwrap_used`), 이유 없는 lint 끄기(`allow_attributes_without_reason`), let chains·format 인자(clippy), `#![deny(warnings)]`(소스에 넣지 않고 `build.warnings`로).
