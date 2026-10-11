<div align="center">

<br />

# WHAT TECHNOLOGIES GUIDELINES

Единый свод архитектурных стандартов, правил дизайн-системы, принципов KMP-разработки и использования библиотек экосистемы WHAT Technologies.

<br />

<img src="https://img.shields.io/badge/status-active-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/version-1.0.0-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/ecosystem-Compose_Multiplatform-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/organization-WHAT_Technologies-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />

[![Organization](https://img.shields.io/badge/GitHub-whatrushki-09090b?style=for-the-badge&logo=github&logoColor=white&labelColor=000000)](https://github.com/whatrushki)
&nbsp;
[![Foundation](https://img.shields.io/badge/Library-compose--foundation-09090b?style=for-the-badge&labelColor=000000)](https://github.com/whatrushki/compose-foundation)

<br />
</div>

---

### Назначение репозитория

Данный репозиторий является **единственным источником правды** для всех проектов организации **WHAT Technologies**. Он содержит формализованные правила, архитектурные контракты и дизайн-документы, обязательные для соблюдения как разработчиками, так и AI-агентами (LLM, Cursor, Copilot).

Цель правил:
1. **Исключение велосипедов:** Запрет на написание кастомного кода для задач, уже решенных в `compose-foundation` (хуки, хранилище, навигация, диалоги, логирование, иконки).
2. **Чистая мультиплатформенность:** Полный запрет на механизм `expect/actual` в бизнес-логике и UI. Вся платформенная специфика изолируется через **интерфейсы и внедрение зависимостей (Koin DI)**.
3. **Единая дизайн-система:** Строгая темная эстетика (нейтральные тона `#09090b`), поддержка нескольких стилей тем (`Monochrome`, `Brand Navy`, `Material You Dynamic`, `Plum`), единые скругления `shapes.large` и фирменные анимации.
4. **Предсказуемый MVI:** Строгий контракт однонаправленного потока данных `UIController<State, Action, Event>`.

---

### Навигация по разделам

#### 1. Архитектура и стандарты KMP (`architecture/`)
* [DI и интерфейсы ВМЕСТО expect/actual](architecture/DI_AND_INTERFACES.md) — фундаментальное правило: никаких `expect/actual` в фичах, только чистые интерфейсы в `commonMain` и реализация через Koin.
* [MVI UIController и состояние](architecture/MVI_UI_CONTROLLER.md) — шаблон `UIController<State, Action, Event>`, `collectStates()`, канал `CollectAction { }`, контракт `RemoteState`.
* [Реактивные хуки состояния](architecture/STATE_HOOKS.md) — хуки `useState`, `useSave`, `useChange`, `useInterval` (Foreground Guard). Полный запрет на boilerplate `remember { mutableStateOf(...) }`.
* [Навигация и глобальные окна](architecture/NAVIGATION.md) — `NavigationHost`, вложенная навигация, `BottomNavBar`, стековые контроллеры `ProvideGlobalDialog` и `ProvideGlobalSheet`.
* [Хранилище настроек и шифрование](architecture/PREFERENCES.md) — реактивное хранилище `PreferenceStorage`, аппаратное шифрование AES-GCM (Android Keystore), делегаты `boolean`, `string`, `model<T>`.

#### 2. Дизайн-система (`design/`)
* [Философия и геометрия](design/PHILOSOPHY.md) — формы, скругления `shapes.large`, обязательный `.clip(...)` при скролле, безопасные зоны `systemBarsPadding()`.
* [Многотемность и цветовая палитра](design/THEMES_AND_PALETTE.md) — спецификация стилей тем (`Monochrome`, `Brand Navy #1F2137`, `Dynamic Material You`, `Plum`), семантические токены `surfaceContainer`, `surfaceBright`, `surfaceDim`.
* [Компоненты интерфейса](design/COMPONENTS.md) — правила верстки, `SearchBox`, `StyledTextField`, фоновые анимации `LiquidBackground`, `Shimmer`.
* [Векторные иконки WHATIcons](design/ICONS.md) — полный каталог и правила использования фирменных иконок из `foundation-ui`.

#### 3. Библиотека Compose Foundation (`foundation/`)
* [Каталог 14 модулей и Do vs Don't](foundation/MODULES_CATALOG.md) — таблица соответствия готовых решений библиотек и запрещенных велосипедов.
* [Главные антипаттерны](foundation/ANTI_PATTERNS.md) — топ-10 ошибок, которые категорически запрещено допускать в кодовой базе.

#### 4. Процессы разработки (`process/`)
* [Чеклист коммита и релизов](process/COMMIT_AND_RELEASE.md) — порядок проверок, семантическое версионирование, выпуск GitHub Release и RuStore.
* [Стандарты оформления README](process/README_STANDARDS.md) — правила визуального оформления репозиториев (Shields badges `#09090b`, отсутствие эмодзи).

#### 5. Конфигурация для AI / IDE (`configs/`)
* [.cursorrules](configs/.cursorrules) — готовый файл правил для Cursor IDE / AI ассистентов.

---

<div align="center">
<sub>© WHAT Technologies. Все права защищены.</sub>
</div>
