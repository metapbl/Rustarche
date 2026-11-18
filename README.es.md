<p align="center">
  <strong>Language:</strong>
  <a href="README.md">English</a> |
  <a href="README.ko.md">한국어</a> |
  <a href="README.zh-CN.md">简体中文</a> |
  <a href="README.ja.md">日本語</a> |
  <a href="README.es.md">Español</a> |
  <a href="README.ru.md">Русский</a>
</p>

<p align="center"><sub>Este documento es una traducción automática con IA de la versión en inglés. Si hay diferencias, prevalece la versión en inglés.</sub></p>

# Rustarche

<p align="center">
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue?style=flat-square"></a>
  <a href="#instalación"><img alt="Claude Code skill" src="https://img.shields.io/badge/Claude_Code-skill-D97757?style=flat-square&logo=claude&logoColor=white"></a>
  <a href="#requisitos"><img alt="Rust 1.97 or later" src="https://img.shields.io/badge/Rust-1.97%2B-B7410E?style=flat-square&logo=rust&logoColor=white"></a>
  <a href="#uso-con-metarche"><img alt="Language skill for Metarche" src="https://img.shields.io/badge/Metarche-language_skill-555555?style=flat-square"></a>
</p>

Rust + archē: una skill de Claude Code con plantillas de Cargo, ajustes de lint, comandos de comprobación y reglas de CLAUDE.md para Rust.

> [!NOTE]
> Las instrucciones de la skill están en inglés. Las reglas y notas que añade a tu proyecto se escriben en tu idioma.

## Qué hace
- **new**: crea un workspace de Cargo con lints, versión fija de la toolchain y perfiles de desarrollo a partir de las plantillas.
- **adopt**: añade los mismos ajustes y reglas de CLAUDE.md a código Rust existente sin sobrescribir archivos.
- **refactor**: primero fija el comportamiento actual y luego cambia la estructura; también cubre portar código de otros lenguajes a Rust.
- Mantiene CLAUDE.md corto: la mayoría de las reglas las imponen Clippy y la configuración de Cargo, y solo se escriben como texto las trampas que el modelo no conoce.
- Carga las notas sobre async, unsafe, FFI y rendimiento solo cuando ese código está en juego.

## Requisitos
- Rust 1.97 o posterior (`build.warnings`). Comprobado con Rust 1.100.0 en Windows (MSVC).
- [Claude Code](https://code.claude.com/docs)
- git (recomendado)

## Instalación
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
Está listo cuando `rustarche` aparece en `/skills` dentro de Claude Code. Para actualizar, ejecuta `git pull` en la carpeta del repositorio.

## Uso
| Comando | Cuándo |
|---|---|
| `/rustarche` | Sin argumento: `new` si no hay `Cargo.toml`, si no `adopt` |
| `/rustarche new` | Crear un workspace de Rust nuevo |
| `/rustarche adopt` | Añadir ajustes de lint y toolchain y reglas de CLAUDE.md a código Rust existente |
| `/rustarche refactor` | Refactorizar código Rust, o portar código de otro lenguaje a Rust |

Claude también puede cargar la skill por sí mismo cuando la petición trata de un proyecto Rust.

Comprobaciones que usa toda la skill:
~~~
cargo fmt --all --check
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
cargo test --workspace
~~~

## Uso con Metarche
En un proyecto de [Metarche](https://github.com/metapbl/Metarche), Metarche lleva el flujo de trabajo y lee `skill/references/metarche.md` para obtener los valores de Rust: preguntas de la entrevista, la comprobación por archivo (`rustfmt --check --edition 2024`), la comprobación al final del turno (Clippy con `build.warnings`), las líneas para CLAUDE.md y la primera tarea del plan. No hace falta llamar a `/rustarche` por separado.

## Estructura
~~~
skill/
├─ SKILL.md              punto de entrada: comprobación de versión, detección de modo, pasos
├─ references/
│  ├─ rules.md           comandos y reglas para CLAUDE.md
│  ├─ metarche.md        valores para proyectos de Metarche
│  ├─ refactor.md        refactorización y portado desde otros lenguajes
│  ├─ async.md           cuándo usar async y reglas si lo usas
│  └─ unsafe-perf.md     unsafe, FFI y rendimiento
└─ templates/            Cargo.toml.workspace, rust-toolchain.toml, clippy.toml, gitignore
~~~

## Créditos
Las fuentes en las que se basa esta skill están en [`NOTICE`](NOTICE).

## Licencia
MIT
