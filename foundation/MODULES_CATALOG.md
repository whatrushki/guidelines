<div align="center">

<br />

# КАТАЛОГ МОДУЛЕЙ COMPOSE FOUNDATION

Полный справочник 14 модулей библиотеки и таблица соответствия готовых решений и запрещенных велосипедов.

<br />

<img src="https://img.shields.io/badge/library-compose--foundation-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/version-1.1.6-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### Таблица модулей экосистемы

| Модуль | Артефакт | Ключевые классы | Заменяемый самописный код |
| :--- | :--- | :--- | :--- |
| **`:compose-foundation`** | `app.what.foundation:compose-foundation` | Umbrella-зависимость | Ручное подключение 10+ зависимостей |
| **`:foundation-core`** | `app.what.foundation:foundation-core` | `UIController`, `AppLogger.Auditor`, `RemoteState`, `Platform` | Ручные MVI-базовые классы, `println`, `Log.d` |
| **`:foundation-state`** | `app.what.foundation:foundation-state` | `useState`, `useSave`, `useChange`, `useInterval` | `remember { mutableStateOf(...) }`, утекающие корутины |
| **`:foundation-preferences`** | `app.what.foundation:foundation-preferences` | `PreferenceStorage`, `AndroidPreferenceEncryptor` | `SharedPreferences`, `DataStore`, ручное шифрование |
| **`:foundation-ui`** | `app.what.foundation:foundation-ui` | `WHATTheme`, `WHATIcons`, `LiquidBackground`, `SearchBox`, `Gap` | Самописные поля ввода, случайные SVG-иконки, шейдеры |
| **`:foundation-navigation`** | `app.what.foundation:foundation-navigation` | `NavigationHost`, `ProvideGlobalDialog`, `ProvideGlobalSheet`, `BottomNavBar` | Voyager, тяжелый decompose, вложенные `Dialog { }` |
| **`:foundation-network`** | `app.what.foundation:foundation-network` | `NetworkMonitor`, `NetworkMonitorPlugin` | Ручные перехватчики Ktor, ConnectivityManager |
| **`:foundation-updater`** | `app.what.foundation:foundation-updater` | `GitHubUpdateManager`, `UpdateView`, `ReleaseNotes` | Ручной парсинг GitHub API, менеджеры скачивания APK |
| **`:foundation-updater-rustore`**| `app.what.foundation:foundation-updater-rustore`| `RuStoreUpdateManager` | Ручная интеграция RuStore App Update SDK |
| **`:foundation-crash`** | `app.what.foundation:foundation-crash` | `CrashHandler`, `CrashScreen`, Bootloop Guard | Падение ОС с системным диалогом, потеря стектрейса |
| **`:foundation-firebase`** | `app.what.foundation:foundation-firebase` | `FirebaseAnalyticsTracker`, `FirebaseCrashReporter` | Прямая жесткая зависимость KMP от Google Play Services |
| **`:foundation-scraper`** | `app.what.foundation:foundation-scraper` | Ksoup-lite HTML Parser | JSoup (который не работает на iOS и Web) |
| **`:foundation-healthcheck`**| `app.what.foundation:foundation-healthcheck`| `HealthcheckClient`, `HealthIndicator` | Ручные пинги эндпоинтов в фоне |
| **`:foundation-delivery`** | `app.what.foundation:foundation-delivery` | `DeliveryEngine`, `SyncCoordinator` | Ручная синхронизация локальной базы данных |

---

### Do vs Don't: Прямые запреты

1. **Логирование:**
   * ❌ `Log.d("TAG", "Message")` / `println("Message")`
   *  `AppLogger.Auditor.info("TAG", "Message")`

2. **Состояние в Composable:**
   * ❌ `val count = remember { mutableStateOf(0) }`
   *  `var count by useState(0)`

3. **Сетевые логгеры:**
   * ❌ `install(Logging)` (засоряет логкат чувствительными токенами и паролями)
   *  `install(NetworkMonitor.Plugin)` (потокобезопасный кольцевой буфер с авто-маскировкой `Authorization` и `Cookie`)

4. **Парсинг HTML в KMP:**
   * ❌ `org.jsoup.Jsoup` (крашит сборку под iOS и Web)
   *  `app.what.foundation:foundation-scraper` (легковесный мультиплатформенный движок Ksoup)

5. **Иконки:**
   * ❌ Случайные сторонние иконки (FontAwesome, Feather, кастомные несогласованные SVG)
   *  `Material Icons` для стандартных UI-действий + `WHATIcons.*` для доменных сущностей (университеты, новости, APK, логи)

<br />

<div align="center">
<sub>© WHAT Technologies. Каталог стандартов.</sub>
</div>
