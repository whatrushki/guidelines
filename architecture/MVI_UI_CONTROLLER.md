<div align="center">

<br />

# АРХИТЕКТУРА: MVI И UICONTROLLER

Стандарт реализации архитектурного паттерна Model-View-Intent на базе класса `UIController` из модуля `foundation-core`.

<br />

<img src="https://img.shields.io/badge/architecture-MVI-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/controller-UIController-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/async-RemoteState-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### Архитектурный контракт MVI

Все экраны и фичи строятся на строгом однонаправленном потоке данных (UDF):

```
       Пользовательское действие (Event)
[UI] ──────────────────────────────────────────> [UIController]
  ▲                                                    │
  │     1. Реактивное состояние (StateFlow<State>)     │
  ├────────────────────────────────────────────────────┤
  │     2. Однократный сайд-эффект (Channel<Action>)   │
  └────────────────────────────────────────────────────┘
```

#### Триада MVI:
1. **`State`**: Неизменяемый (`data class`) снимок всего состояния экрана. Обязан иметь значения по умолчанию для всех полей.
2. **`Action`**: Однократные сайд-эффекты (`sealed interface`), которые не должны переживать пересоздание экрана (показ снэкбара/тоста, навигационный переход, открытие внешней ссылки). Передаются через буферизированный канал `Channel.BUFFERED`.
3. **`Event`**: Намерения пользователя (`sealed interface`), передаваемые в метод `obtainEvent(event)`.

---

### Контракт удаленной загрузки: RemoteState

Для отображения асинхронных операций (загрузка расписания, профиля, новостей) используется стандартный `RemoteState` из `foundation-core`:

```kotlin
sealed interface RemoteState {
    object Idle : RemoteState
    object Loading : RemoteState
    class Error(val e: Exception) : RemoteState
    object Success : RemoteState
    object Empty : RemoteState
}
```

---

### Эталонная реализация UIController

```kotlin
package app.what.schedule.features.schedule

import androidx.lifecycle.viewModelScope
import app.what.foundation.core.UIController
import app.what.foundation.data.RemoteState
import kotlinx.coroutines.launch

// 1. Неизменяемое состояние
data class ScheduleState(
    val query: String = "",
    val lessons: List<LessonDto> = emptyList(),
    val loadState: RemoteState = RemoteState.Idle
)

// 2. Однократные действия (Action)
sealed interface ScheduleAction {
    data class ShowToast(val message: String) : ScheduleAction
    data class OpenLessonDetail(val lessonId: String) : ScheduleAction
}

// 3. Пользовательские события (Event)
sealed interface ScheduleEvent {
    data class OnQueryChange(val newQuery: String) : ScheduleEvent
    data object Refresh : ScheduleEvent
    data class OnLessonClick(val lessonId: String) : ScheduleEvent
}

// 4. Контроллер
class ScheduleController(
    private val repository: ScheduleRepository
) : UIController<ScheduleState, ScheduleAction, ScheduleEvent>(ScheduleState()) {

    init {
        obtainEvent(ScheduleEvent.Refresh)
    }

    override fun obtainEvent(viewEvent: ScheduleEvent) {
        when (viewEvent) {
            is ScheduleEvent.OnQueryChange -> {
                updateState { copy(query = viewEvent.newQuery) }
            }
            is ScheduleEvent.Refresh -> {
                loadSchedule()
            }
            is ScheduleEvent.OnLessonClick -> {
                emitAction(ScheduleAction.OpenLessonDetail(viewEvent.lessonId))
            }
        }
    }

    private fun loadSchedule() {
        viewModelScope.launch {
            updateState { copy(loadState = RemoteState.Loading) }
            try {
                val data = repository.getSchedule()
                updateState { 
                    copy(
                        lessons = data,
                        loadState = if (data.isEmpty()) RemoteState.Empty else RemoteState.Success
                    )
                }
            } catch (e: Exception) {
                updateState { copy(loadState = RemoteState.Error(e)) }
                emitAction(ScheduleAction.ShowToast("Ошибка загрузки: ${e.message}"))
            }
        }
    }
}
```

---

### Подключение в Composable-экране

> **ПРАВИЛО СБОРА ДЕЙСТВИЙ И СОСТОЯНИЯ:**  
> Используйте `controller.collectStates()` для состояния и `controller.CollectAction { action -> ... }` для действий.  
> `CollectAction` автоматически привязывается к `Lifecycle.State.STARTED`, предотвращая потерю или дублирование событий в фоновом режиме.

```kotlin
@Composable
fun ScheduleScreen(
    controller: ScheduleController = koinInject()
) {
    val state by controller.collectStates()

    // Безопасная обработка однократных событий с учетом Lifecycle:
    controller.CollectAction { action ->
        when (action) {
            is ScheduleAction.ShowToast -> showToast(action.message)
            is ScheduleAction.OpenLessonDetail -> navigateToDetail(action.lessonId)
        }
    }

    Column(modifier = Modifier.fillMaxSize()) {
        SearchBox(
            value = state.query,
            onValueChange = { controller.obtainEvent(ScheduleEvent.OnQueryChange(it)) }
        )

        when (state.loadState) {
            is RemoteState.Loading -> CircularProgressIndicator()
            is RemoteState.Empty -> Text("Занятий нет")
            is RemoteState.Error -> Text("Ошибка")
            is RemoteState.Success, is RemoteState.Idle -> {
                LazyColumn {
                    items(state.lessons) { lesson ->
                        LessonItem(
                            lesson = lesson,
                            onClick = { controller.obtainEvent(ScheduleEvent.OnLessonClick(lesson.id)) }
                        )
                    }
                }
            }
        }
    }
}
```

<br />

<div align="center">
<sub>© WHAT Technologies. Архитектурный стандарт MVI.</sub>
</div>
