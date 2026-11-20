<p align="center">
  <strong>Language:</strong>
  <a href="README.md">English</a> |
  <a href="README.ko.md">한국어</a> |
  <a href="README.zh-CN.md">简体中文</a> |
  <a href="README.ja.md">日本語</a> |
  <a href="README.es.md">Español</a> |
  <a href="README.ru.md">Русский</a>
</p>

<p align="center"><sub>Этот документ переведён с английской версии с помощью ИИ. При расхождениях ориентируйтесь на английскую версию.</sub></p>

# Rustarche

<p align="center">
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-blue?style=flat-square"></a>
  <a href="#установка"><img alt="Agent Skill for Claude Code and Codex" src="https://img.shields.io/badge/Agent_Skill-Claude_Code_%7C_Codex-D97757?style=flat-square"></a>
  <a href="#требования"><img alt="Rust 1.97 or later" src="https://img.shields.io/badge/Rust-1.97%2B-B7410E?style=flat-square&logo=rust&logoColor=white"></a>
  <a href="#использование-с-metarche"><img alt="Language skill for Metarche" src="https://img.shields.io/badge/Metarche-language_skill-555555?style=flat-square"></a>
</p>

Rust + archē: навык для агентов с шаблонами Cargo, настройками линтеров, командами проверки и правилами AGENTS.md для Rust.

> [!NOTE]
> Инструкции навыка написаны на английском. Правила и заметки, которые он добавляет в проект, пишутся на вашем языке.

## Что делает
- **new**: создаёт рабочее пространство Cargo с линтами, закреплённой версией инструментов и профилями разработки по шаблонам.
- **adopt**: добавляет те же настройки и правила AGENTS.md в существующий код на Rust, не перезаписывая файлы.
- **refactor**: сначала фиксирует текущее поведение, затем меняет структуру; также охватывает перенос кода с других языков на Rust.
- Держит AGENTS.md коротким: большинство правил обеспечивают Clippy и настройки Cargo, а текстом записываются только ловушки, о которых модель не знает.
- Заметки об async, unsafe, FFI и производительности загружаются только тогда, когда дело касается такого кода.

## Требования
- Rust 1.97 или новее (`build.warnings`). Проверено на Rust 1.100.0 в Windows (MSVC).
- Агент для программирования, который читает Agent Skills и AGENTS.md: [Claude Code](https://code.claude.com/docs) (проверено) или [Codex](https://learn.chatgpt.com/docs/build-skills).
- git (рекомендуется)

## Установка
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
Codex (та же папка в `~/.agents/skills`), Windows:
~~~
New-Item -ItemType Directory -Force "$env:USERPROFILE\.agents\skills" | Out-Null
New-Item -ItemType Junction -Path "$env:USERPROFILE\.agents\skills\rustarche" -Target "$env:USERPROFILE\My Projects\Rustarche\skill"
~~~
macOS / Linux:
~~~
mkdir -p ~/.agents/skills && ln -s ~/Rustarche/skill ~/.agents/skills/rustarche
~~~
Готово, когда `rustarche` появится в `/skills` в Claude Code или Codex. Для обновления выполните `git pull` в папке репозитория.

## Быстрый старт
В пустой папке запустите агента и вызовите навык (`$rustarche new` в Codex):
~~~
> /rustarche new
~~~
Навык показывает шаблоны и спрашивает, прежде чем что-то создавать. После одобрения он создаёт рабочее пространство и запускает три проверки. Новое рабочее пространство проходит их все:
~~~
cargo fmt --all --check                                               # prints nothing, exit 0
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'  # ends with: Finished `dev` profile
cargo test --workspace                                                # test result: ok.
~~~

## Использование
| Команда | Когда |
|---|---|
| `/rustarche` | Без аргумента: `new`, если нет `Cargo.toml`, иначе `adopt` |
| `/rustarche new` | Создать новое рабочее пространство Rust |
| `/rustarche adopt` | Добавить настройки линтов и инструментов и правила AGENTS.md в существующий код на Rust |
| `/rustarche refactor` | Рефакторинг кода на Rust или перенос кода с другого языка на Rust |

Агент может и сам загрузить навык, если запрос касается проекта на Rust. В Codex навык вызывается как `$rustarche` (например, `$rustarche new`).

Проверки, которые использует навык:
~~~
cargo fmt --all --check
cargo clippy --workspace --all-targets --config 'build.warnings="deny"'
cargo test --workspace
~~~

## Использование с Metarche
В проекте [Metarche](https://github.com/metapbl/Metarche) процессом управляет Metarche, а значения для Rust он берёт из `skill/references/metarche.md`: вопросы интервью, проверку для файла (`rustfmt --check --edition 2024`), проверку в конце хода (Clippy с `build.warnings`), строки для AGENTS.md и первую задачу плана. Вызывать `/rustarche` отдельно не нужно.

## Структура
~~~
skill/
├─ SKILL.md              точка входа: проверка версии, выбор режима, шаги
├─ references/
│  ├─ rules.md           команды и правила для AGENTS.md
│  ├─ metarche.md        значения для проектов Metarche
│  ├─ refactor.md        рефакторинг и перенос с других языков
│  ├─ async.md           когда вводить async и правила, если он введён
│  └─ unsafe-perf.md     unsafe, FFI и производительность
└─ templates/            Cargo.toml.workspace, rust-toolchain.toml, clippy.toml, gitignore
~~~

## Состояние проекта
- Проверено на Rust 1.100.0 в Windows (MSVC) с Claude Code: новое рабочее пространство из шаблонов проходит форматирование, линты и тесты.
- В Codex и на большой кодовой базе ещё не проверялось.

## Помощь и участие
- Вопросы и сообщения об ошибках: [GitHub Issues](https://github.com/metapbl/Rustarche/issues).
- Pull request: сначала откройте issue и согласуйте изменение. Текст навыка — на английском; новое рабочее пространство из шаблонов должно проходить три проверки из быстрого старта.

## Безопасность
Сообщайте об уязвимостях приватно через [приватные отчёты об уязвимостях GitHub](https://github.com/metapbl/Rustarche/security/advisories/new), а не в публичном issue.

## Благодарности
Источники, на которые опирается навык, перечислены в [`NOTICE`](NOTICE).

## Лицензия
MIT
