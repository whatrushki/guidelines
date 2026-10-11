<div align="center">

<br />

# АНТИПАТТЕРНЫ И СТРОГИЕ ЗАПРЕТЫ

Топ-10 архитектурных и визуальных ошибок, которые категорически запрещено допускать в проектах WHAT Technologies.

<br />

<img src="https://img.shields.io/badge/status-mandatory-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/rules-zero_tolerance-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### 1. Использование expect/actual в фичах
* **Ошибка**: Создание `expect class DeviceVibrator` или `expect fun shareText(...)` внутри feature-модуля.
* **Почему плохо**: Ломает Clean Architecture, нарушает DIP (Dependency Inversion Principle), делает невозможным модульное тестирование без виртуальных устройств.
* **Как правильно**: Чистый Kotlin интерфейс в `commonMain` + платформа-специфичная реализация в `androidMain`/`jvmMain` + инъекция через Koin DI.

### 2. Boilerplate `remember { mutableStateOf(...) }`
* **Ошибка**: Нагромождение `val x = remember { mutableStateOf("") }`.
* **Почему плохо**: Избыточный шум в коде, отсутствие поддержки сброса по ключу или чистого сохранения.
* **Как правильно**: Всегда использовать `var x by useState("")` или `var x by useSave("")`.

### 3. Локальные `Dialog` и `ModalBottomSheet` в карточках
* **Ошибка**: Помещение вызова `Dialog { ... }` прямо внутрь верстки карточки элемента списка.
* **Почему плохо**: Утечки памяти, проблемы со стеком навигации, невозможность управлять модальными окнами из контроллера.
* **Как правильно**: Обертка `ProvideGlobalDialog` / `ProvideGlobalSheet` на уровне хоста и открытие через `LocalDialogController.current.open { ... }`.

### 4. Нескругленные скролл-контейнеры (Квадратный срез)
* **Ошибка**: `LazyRow` или `LazyColumn` со скроллируемыми карточками без модификатора `.clip(...)` на родительском контейнере.
* **Почему плохо**: Элементы при скролле уходят под край экрана и обрезаются острыми прямоугольными углами.
* **Как правильно**: Всегда оборачивать в `Box(Modifier.clip(MaterialTheme.shapes.large))`.

### 5. Хардкод цветов вместо семантических токенов темы
* **Ошибка**: Использование `Color(0xFF222222)`, `Color.White`, `Color.Black` в верстке.
* **Почему плохо**: Ломает многотемность (`Monochrome`, `Brand`, `Dynamic`, `Plum`). В светлой теме интерфейс становится нечитаемым.
* **Как правильно**: Всегда использовать токены `MaterialTheme.colorScheme.surfaceContainer`, `background`, `onSurface`, `outlineVariant`.

### 6. Ручное сохранение настроек через SharedPreferences / DataStore
* **Ошибка**: Создание собственных репозиториев настроек на сыром Android `SharedPreferences`.
* **Почему плохо**: Отсутствие шифрования, отсутствие мультиплатформенности, лишний асинхронный бойлерплейт.
* **Как правильно**: Наследование от `PreferenceStorage` с делегатами `boolean`, `string`, `model<T>` и флагом `isEncrypted = true`.

### 7. Небезопасный `LaunchedEffect` без пропуска первого запуска
* **Ошибка**: `LaunchedEffect(query) { sendSearchApi(query) }`.
* **Почему плохо**: Срабатывает сразу при монтировании экрана с пустой строкой, отправляя ненужный сетевой запрос.
* **Как правильно**: `useChange(query, skipInitial = true) { sendSearchApi(it) }`.

### 8. Сторонние разношерстные библиотеки иконок
* **Ошибка**: Подключение сторонних библиотек вроде FontAwesome, Feather или использование несогласованных самописных SVG.
* **Почему плохо**: Разная толщина линий, визуальный диссонанс, увеличение веса сборки.
* **Как правильно**: Использовать `Material Icons` для стандартных системных действий и каталог `WHATIcons` из `foundation-ui` для брендовых и специализированных сущностей.

### 9. Утечка Android Context в commonMain
* **Ошибка**: Передача `Context` аргументом в методы общих классов или UseCase.
* **Почему плохо**: Ломает компиляцию под iOS, Desktop и Wasm.
* **Как правильно**: `Context` живет строго в `androidMain` внутри реализации интерфейса, предоставленной через Koin `androidContext()`.

### 10. `println()` и сырой `android.util.Log`
* **Ошибка**: Вызов `println(...)` в коде бизнес-логики.
* **Почему плохо**: Логи не попадают в систему аудита, не экспортируются при падении приложения в `CrashScreen`.
* **Как правильно**: `AppLogger.Auditor.info(tag, message)`.

<br />

<div align="center">
<sub>© WHAT Technologies. Список антипаттернов.</sub>
</div>
