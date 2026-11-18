---
name: rustarche
description: "Rust 프로젝트를 새로 시작하거나(new), 기존 Rust 코드에 설정·지침을 입히거나(adopt), Rust 코드를 리팩토링하거나 Python·C++ 등 다른 언어 코드를 Rust로 옮길 때(refactor) 쓰는 Rust 전용 값 모음. Cargo 워크스페이스·lint·툴체인 템플릿, CLAUDE.md용 Rust 규칙, 검사 명령을 준다. metarche 프로젝트에서는 절차를 metarche에 맡기고 값만 준다. Use when starting, adopting or refactoring a Rust (Cargo) project, or porting code to Rust."
argument-hint: "[new|adopt|refactor]"
---

# Rustarche — Rust + archē

절차(질의응답, 승인, 기록)는 맡지 않는다. Rust에 필요한 값과 규칙만 준다.
근거는 저장소 `NOTICE`의 조사 보고서다. 규칙 대부분은 lint와 Cargo 설정으로 강제하고, 문장으로 남기는 것은 모델이 모르는 함정과 판단 규칙뿐이다.

## 기준 버전
- 이 스킬은 **Rust 1.100.0 (2026-11)** 기준으로 확인했다. 시작할 때 `rustc --version`을 보고 다르면 알린다.
  - 1.97 미만이면 `build.warnings`가 없다. 경고 차단은 `cargo clippy ... -- -D warnings`로 바꾼다.
  - 더 새 버전이면 그사이 바뀐 기능은 반영돼 있지 않다고 알린다.
- crate API는 기억에 기대지 않고 docs.rs나 `cargo doc --no-deps`로 확인한다 (새 API에서 모델 정답률이 크게 떨어진다는 측정이 있다).

## 시작: 판별
1. 루트 `CLAUDE.md` 첫 줄이 `<!-- metarche`로 시작하면 **metarche 프로젝트**다. 훅 설치·상태 기록·승인은 metarche가 한다. metarche가 부르는 대로 `references/metarche.md`의 값만 준다. 이 파일의 아래 절차는 쓰지 않는다.
2. 아니면 인자대로, 없으면 폴더 상태로 모드를 고른다: `Cargo.toml`이 없으면 new, 있으면 adopt. 리팩토링이나 이전을 요청받았으면 refactor.
3. 판별 결과와 할 일을 한 줄로 알리고 확인을 받는다.

## new / adopt (metarche가 아닐 때)
1. `templates/`를 보여 주고 무엇을 만들지 승인받는다. 이미 있는 파일은 덮어쓰지 않고 차이를 보인다.
   - `Cargo.toml.workspace` → 루트 `Cargo.toml` (가상 매니페스트, lint·프로필 포함)
   - `rust-toolchain.toml`, `clippy.toml`, `gitignore` → `.gitignore`에 합친다
2. crate는 `cargo new --lib crates/<이름>`(또는 `--bin`)으로 만든다. 워크스페이스 안에서는 edition·rust-version 상속과 `[lints] workspace = true`가 자동으로 들어간다.
   - `cargo new`가 만든 견본 함수는 pedantic lint(`must_use_candidate`)에 바로 걸린다. 지우거나 고친다.
3. 루트 `CLAUDE.md`에 `references/rules.md`의 "명령"과 "규칙" 절을 옮긴다. 기존 CLAUDE.md가 있으면 겹치는 줄은 넣지 않는다.
4. 확인: 아래 세 명령이 모두 exit 0.
   ~~~
   cargo fmt --all --check
   cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
   cargo test --workspace
   ~~~
5. 편집 때·작업 끝 자동 검사(훅)는 이 스킬에 없다. metarche가 설치돼 있으면 그 `references/templates/hooks/`의 check·finish 훅에 `references/metarche.md` §2의 명령을 넣어 쓰자고 제안한다.

## refactor
`references/refactor.md`를 읽고 따른다. metarche 프로젝트가 아니면 진행 상태를 루트 `REFACTOR.md`에 적는다 (고정할 동작, 단계, 남은 일). CLAUDE.md에는 `@` 임포트 없이 경로만 적는다.

## 필요할 때만 읽는 파일
| 파일 | 언제 |
|---|---|
| `references/rules.md` | CLAUDE.md를 만들거나 고칠 때 |
| `references/metarche.md` | metarche의 discover·docs·plan 단계 |
| `references/refactor.md` | 리팩토링·다른 언어에서 이전 |
| `references/async.md` | async를 들일지 정하거나 async 코드를 고칠 때 |
| `references/unsafe-perf.md` | unsafe, FFI, 성능 최적화를 다룰 때 |
