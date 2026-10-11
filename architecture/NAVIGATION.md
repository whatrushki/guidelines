<div align="center">

<br />

# АРХИТЕКТУРА: НАВИГАЦИЯ И ГЛОБАЛЬНЫЕ ОКНА

Модульная типобезопасная навигация, реактивный контекст, контроллеры диалогов и шторок из модуля `foundation-navigation`.

<br />

<img src="https://img.shields.io/badge/library-foundation--navigation-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/architecture-registry_based-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### Архитектура навигационного стека

Модуль `foundation-navigation` построен вокруг концепции **Feature Isolation**:
1. Фичи не знают о реализации других экранов — они оперируют только сериализуемыми маршрутами (`NavProvider`).
2. Регистрация экранов происходит декларативно без тяжеловесной рефлексии через DSL `Registry`.
3. Модальные окна (диалоги и нижние шторки) вынесены в **глобальные хосты** (`ProvideGlobalDialog`, `ProvideGlobalSheet`). Экраны не создают внутри себя Compose-диалоги, а вызывают контроллер из `LocalDialogController.current`.

---

### Объявление маршрутов и регистрация экранов

```kotlin
// 1. Маршруты в модуле фичи:
@Serializable
data object ScheduleRoute : NavProvider()

@Serializable
data class LessonDetailRoute(val lessonId: String) : NavProvider()

// 2. Регистрация экрана в DSL фичи:
fun RegistryBuilder.scheduleRegistry() {
    register(ScheduleRoute::class) {
        ScheduleScreen()
    }
    register(LessonDetailRoute::class) { route ->
        LessonDetailScreen(lessonId = route.lessonId)
    }
}
```

---

### Организация корня приложения (NavigationHost)

В корне приложения создается единая обертка с глобальными диалогами и шторками:

```kotlin
@Composable
fun AppNavigationRoot() {
    ProvideGlobalDialog {
        ProvideGlobalSheet {
            val navigator = rememberHostNavigator()

            NavigationHost(
                start = ScheduleRoute
            ) {
                scheduleRegistry()
                newsRegistry()
                settingsRegistry()
            }
        }
    }
}
```

---

### Работа с глобальными диалогами и шторками

> **ПРАВИЛО ГЛОБАЛЬНЫХ ОКОН:**  
> Категорически запрещено инстанцировать `Dialog(...)` или `ModalBottomSheet(...)` внутри Composable-верстки карточек и элементов списков.  
> Для открытия шторки или диалога используйте `LocalDialogController` или `LocalSheetController`.

```kotlin
@Composable
fun LessonCard(lesson: LessonDto) {
    val dialog = LocalDialogController.current
    val sheet = LocalSheetController.current

    Row(
        modifier = Modifier.clickable {
            // Открытие шторки с деталями:
            sheet.open {
                LessonDetailSheetContent(lesson = lesson, onClose = sheet::close)
            }
        }
    ) {
        Text(lesson.title)

        IconButton(onClick = {
            // Открытие подтверждающего диалога:
            dialog.open {
                ConfirmDeleteDialog(onConfirm = { ... }, onDismiss = dialog::close)
            }
        }) {
            Icon(WHATIcons.Logs, contentDescription = null)
        }
    }
}
```

<br />

<div align="center">
<sub>© WHAT Technologies. Стандарт навигации.</sub>
</div>
