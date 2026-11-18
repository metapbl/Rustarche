<p align="center">
  <strong>Language:</strong>
  <a href="README.md">English</a> |
  <a href="README.ko.md">한국어</a> |
  <a href="README.zh-CN.md">简体中文</a> |
  <a href="README.ja.md">日本語</a> |
  <a href="README.es.md">Español</a> |
  <a href="README.ru.md">Русский</a>
</p>

<p align="center"><sub>이 문서는 영어판을 AI로 번역한 것입니다. 내용이 다르면 영어판을 따릅니다.</sub></p>

# Rustarche

<p align="center">
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue?style=flat-square"></a>
  <a href="#설치"><img alt="Claude Code skill" src="https://img.shields.io/badge/Claude_Code-skill-D97757?style=flat-square&logo=claude&logoColor=white"></a>
  <a href="#요구-사항"><img alt="Rust 1.97 or later" src="https://img.shields.io/badge/Rust-1.97%2B-B7410E?style=flat-square&logo=rust&logoColor=white"></a>
  <a href="#metarche와-함께-쓰기"><img alt="Language skill for Metarche" src="https://img.shields.io/badge/Metarche-language_skill-555555?style=flat-square"></a>
</p>

Rust + archē(러스타르케): Rust용 Cargo 템플릿, lint 설정, 검사 명령, CLAUDE.md 규칙을 주는 Claude Code 스킬.

> [!NOTE]
> 스킬의 지시문은 한국어로 쓰여 있어서, 이 스킬이 프로젝트에 더하는 규칙과 메모도 한국어입니다.

## 하는 일
- **new**: 템플릿으로 lint, 툴체인 고정, 개발 프로필이 들어간 Cargo 워크스페이스를 만듭니다.
- **adopt**: 기존 Rust 코드에 같은 설정과 CLAUDE.md 규칙을 더합니다. 있는 파일은 덮어쓰지 않습니다.
- **refactor**: 지금 동작을 먼저 고정한 뒤 구조를 바꿉니다. 다른 언어 코드를 Rust로 옮기는 일도 다룹니다.
- CLAUDE.md를 짧게 유지합니다. 규칙 대부분은 Clippy와 Cargo 설정이 강제하고, 모델이 모르는 함정만 글로 남깁니다.
- async, unsafe, FFI, 성능에 관한 참고는 그 코드를 다룰 때만 읽습니다.

## 요구 사항
- Rust 1.97 이상(`build.warnings`). Windows(MSVC)의 Rust 1.100.0으로 확인했습니다.
- [Claude Code](https://code.claude.com/docs)
- git (권장)

## 설치
Windows (PowerShell 7):
~~~
git clone https://github.com/metapbl/Rustarche.git "$env:USERPROFILE\My Projects\Rustarche"
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills" | Out-Null
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\rustarche" -Target "$env:USERPROFILE\My Projects\Rustarche\skill"
~~~
macOS / Linux:
~~~
git clone https://github.com/metapbl/Rustarche.git ~/Rustarche
mkdir -p ~/.claude/skills && ln -s ~/Rustarche/skill ~/.claude/skills/rustarche
~~~
Claude Code의 `/skills`에 `rustarche`가 보이면 끝입니다. 갱신은 저장소 폴더에서 `git pull`을 실행합니다.

## 사용
| 명령 | 언제 |
|---|---|
| `/rustarche` | 인자 없이: `Cargo.toml`이 없으면 `new`, 있으면 `adopt` |
| `/rustarche new` | 새 Rust 워크스페이스 만들기 |
| `/rustarche adopt` | 기존 Rust 코드에 lint·툴체인 설정과 CLAUDE.md 규칙 더하기 |
| `/rustarche refactor` | Rust 코드 리팩토링, 또는 다른 언어 코드를 Rust로 옮기기 |

Rust 프로젝트에 관한 요청이면 Claude가 스스로 이 스킬을 불러오기도 합니다.

스킬 전체에서 쓰는 검사:
~~~
cargo fmt --all --check
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
cargo test --workspace
~~~

## Metarche와 함께 쓰기
[Metarche](https://github.com/metapbl/Metarche) 프로젝트에서는 Metarche가 절차를 진행하고, Rust 값은 `skill/references/metarche.md`에서 읽어 갑니다. 질의응답 질문, 파일 단위 검사(`rustfmt --check --edition 2024`), 작업 끝 검사(`build.warnings`를 켠 Clippy), CLAUDE.md에 넣을 줄, 계획 1번 작업이 여기에 있습니다. `/rustarche`를 따로 부를 필요가 없습니다.

## 구조
~~~
skill/
├─ SKILL.md              진입점: 버전 확인, 모드 판별, 절차
├─ references/
│  ├─ rules.md           CLAUDE.md에 넣을 명령과 규칙
│  ├─ metarche.md        Metarche 프로젝트에 줄 값
│  ├─ refactor.md        리팩토링과 다른 언어에서 옮기기
│  ├─ async.md           async를 들일지, 들였을 때의 규칙
│  └─ unsafe-perf.md     unsafe, FFI, 성능
└─ templates/            Cargo.toml.workspace, rust-toolchain.toml, clippy.toml, gitignore
~~~

## 출처
이 스킬이 참고한 자료는 [`NOTICE`](NOTICE)에 있습니다.

## 라이선스
MIT
