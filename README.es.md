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
  <a href="#instalación"><img alt="Agent Skill for Claude Code and Codex" src="https://img.shields.io/badge/Agent_Skill-Claude_Code_%7C_Codex-D97757?style=flat-square"></a>
  <a href="#requisitos"><img alt="Rust 1.97 or later" src="https://img.shields.io/badge/Rust-1.97%2B-B7410E?style=flat-square&logo=rust&logoColor=white"></a>
  <a href="#uso-con-metarche"><img alt="Language skill for Metarche" src="https://img.shields.io/badge/Metarche-language_skill-555555?style=flat-square"></a>
</p>

Rust + archē: una skill de agente con plantillas de Cargo, ajustes de lint, comandos de comprobación y reglas de AGENTS.md para Rust.

> [!NOTE]
> Las instrucciones de la skill están en inglés. Las reglas y notas que añade a tu proyecto se escriben en tu idioma.

## Qué hace
- **new**: crea un workspace de Cargo con lints, versión fija de la toolchain y perfiles de desarrollo a partir de las plantillas.
- **adopt**: añade los mismos ajustes y reglas de AGENTS.md a código Rust existente sin sobrescribir archivos.
- **refactor**: primero fija el comportamiento actual y luego cambia la estructura; también cubre portar código de otros lenguajes a Rust.
- Mantiene AGENTS.md corto: la mayoría de las reglas las imponen Clippy y la configuración de Cargo, y solo se escriben como texto las trampas que el modelo no conoce.
- Carga las notas sobre async, unsafe, FFI y rendimiento solo cuando ese código está en juego.

## Requisitos
- Rust 1.97 o posterior (`build.warnings`). Comprobado con Rust 1.100.0 en Windows (MSVC).
- Un agente de programación que lea Agent Skills y AGENTS.md: [Claude Code](https://code.claude.com/docs) (probado) o [Codex](https://learn.chatgpt.com/docs/build-skills).
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
Codex (la misma carpeta bajo `~/.agents/skills`), Windows:
~~~
New-Item -ItemType Directory -Force "$env:USERPROFILE\.agents\skills" | Out-Null
New-Item -ItemType Junction -Path "$env:USERPROFILE\.agents\skills\rustarche" -Target "$env:USERPROFILE\My Projects\Rustarche\skill"
~~~
macOS / Linux:
~~~
mkdir -p ~/.agents/skills && ln -s ~/Rustarche/skill ~/.agents/skills/rustarche
~~~
Está listo cuando `rustarche` aparece en `/skills` dentro de Claude Code o Codex. Para actualizar, ejecuta `git pull` en la carpeta del repositorio.

## Inicio rápido
En una carpeta vacía, inicia tu agente y llama a la skill (`$rustarche new` en Codex):
~~~
> /rustarche new
~~~
Muestra las plantillas y pregunta antes de crear nada. Cuando lo apruebas, crea el workspace y ejecuta las tres comprobaciones. Un workspace nuevo las pasa todas:
~~~
cargo fmt --all --check                                               # prints nothing, exit 0
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'  # ends with: Finished `dev` profile
cargo test --workspace                                                # test result: ok.
~~~

## Uso
| Comando | Cuándo |
|---|---|
| `/rustarche` | Sin argumento: `new` si no hay `Cargo.toml`, si no `adopt` |
| `/rustarche new` | Crear un workspace de Rust nuevo |
| `/rustarche adopt` | Añadir ajustes de lint y toolchain y reglas de AGENTS.md a código Rust existente |
| `/rustarche refactor` | Refactorizar código Rust, o portar código de otro lenguaje a Rust |

El agente también puede cargar la skill por sí mismo cuando la petición trata de un proyecto Rust. En Codex se llama como `$rustarche` (p. ej. `$rustarche new`).

Comprobaciones que usa toda la skill:
~~~
cargo fmt --all --check
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
cargo test --workspace
~~~

## Uso con Metarche
En un proyecto de [Metarche](https://github.com/metapbl/Metarche), Metarche lleva el flujo de trabajo y lee `skill/references/metarche.md` para obtener los valores de Rust: preguntas de la entrevista, la comprobación por archivo (`rustfmt --check --edition 2024`), la comprobación al final del turno (Clippy con `build.warnings`), las líneas para AGENTS.md y la primera tarea del plan. No hace falta llamar a `/rustarche` por separado.

## Estructura
~~~
skill/
├─ SKILL.md              punto de entrada: comprobación de versión, detección de modo, pasos
├─ references/
│  ├─ rules.md           comandos y reglas para AGENTS.md
│  ├─ metarche.md        valores para proyectos de Metarche
│  ├─ refactor.md        refactorización y portado desde otros lenguajes
│  ├─ async.md           cuándo usar async y reglas si lo usas
│  └─ unsafe-perf.md     unsafe, FFI y rendimiento
└─ templates/            Cargo.toml.workspace, rust-toolchain.toml, clippy.toml, gitignore
~~~

## Estado del proyecto
- Comprobado con Rust 1.100.0 en Windows (MSVC) con Claude Code: un workspace nuevo creado con las plantillas pasa formato, lint y pruebas.
- Aún no se ha probado en Codex ni en una base de código grande.

## Ayuda y contribuciones
- Preguntas e informes de errores: [GitHub Issues](https://github.com/metapbl/Rustarche/issues).
- Pull requests: abre primero un issue para acordar el cambio. Mantén el texto de la skill en inglés, y que un workspace nuevo creado con las plantillas pase las tres comprobaciones del inicio rápido.

## Seguridad
Informa de vulnerabilidades en privado mediante el [informe privado de vulnerabilidades de GitHub](https://github.com/metapbl/Rustarche/security/advisories/new), no en un issue público.

## Créditos
Las fuentes en las que se basa esta skill están en [`NOTICE`](NOTICE).

## Licencia
MIT
