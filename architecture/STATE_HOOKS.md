<div align="center">

<br />

# АРХИТЕКТУРА: РЕАКТИВНЫЕ ХУКИ СОСТОЯНИЯ

Стандарты работы с локальным состоянием и сайд-эффектами в Compose Multiplatform из модуля `foundation-state`.

<br />

<img src="https://img.shields.io/badge/library-foundation--state-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/paradigm-declarative_hooks-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### Принцип лаконичности (DX)

> **КАТЕГОРИЧЕСКИЙ ЗАПРЕТ БОЙЛЕРПЛЕЙТА:**  
> В коде приложений WHAT Technologies **ЗАПРЕЩЕНО** использовать многословные конструкции `remember { mutableStateOf(...) }` и ручные бесконечные циклы `while(isActive) delay(...)`.  
> Всегда используются декларативные хуки из модуля `:foundation-state`.

---

### Сравнительная таблица хуков

| Задача | Старый Compose бойлерплейт (ЗАПРЕЩЕНО) | WHAT Foundation хук (ОБЯЗАТЕЛЬНО) |
| :--- | :--- | :--- |
| **Локальное состояние** | `val s = remember { mutableStateOf("hi") }` | `var s by useState("hi")` |
| **Состояние с сохранением** | `val s = rememberSaveable { mutableStateOf(0) }` | `var s by useSave(0)` |
| **Реакция на изменение** | `LaunchedEffect(query) { ... }` (срабатывает при старте) | `useChange(query, skipInitial = true) { ... }` |
| **Мульти-реакция** | `LaunchedEffect(a, b) { ... }` | `useChange(a, b, skipInitial = true) { ... }` |
| **Периодический таймер** | Бесконечный `while(true) delay()` (утечка в фоне) | `val time by useInterval(0, delaySeconds = 5) { it + 1 }` |

---

### Примеры использования

#### 1. useState и сброс по ключу
```kotlin
@Composable
fun FilterView(userId: String) {
    // Состояние автоматически сбросится, если изменится userId:
    var currentFilter by useState("all", key = userId)

    Button(onClick = { currentFilter = "active" }) {
        Text("Фильтр: $currentFilter")
    }
}
```

#### 2. useChange с пропуском начальной композиции
Стандартный `LaunchedEffect` всегда срабатывает при первой отрисовке экрана, что часто приводит к ложным сетевым запросам. `useChange` с флагом `skipInitial = true` срабатывает **только** тогда, когда пользователь реально изменил значение:

```kotlin
@Composable
fun SearchComponent(onSearch: (String) -> Unit) {
    var query by useState("")

    // Не вызывается при первом открытии экрана! Только при вводе текста:
    useChange(query, skipInitial = true) { currentQuery ->
        onSearch(currentQuery)
    }

    SearchBox(value = query, onValueChange = { query = it })
}
```

#### 3. useInterval (Встроенная защита Foreground Guard)
Хук `useInterval` автоматически приостанавливает выполнение таймера, когда приложение уходит в фоновый режим (`ON_PAUSE`), и возобновляет при возврате (`ON_RESUME`), не разряжая батарею устройства:

```kotlin
@Composable
fun LiveStatusIndicator() {
    // Обновляет статус каждые 10 секунд только пока экран активен:
    val ping by useInterval(initialValue = 0, delaySeconds = 10) { 
        checkServerPing() 
    }

    Text("Пинг сервера: $ping мс")
}
```

<br />

<div align="center">
<sub>© WHAT Technologies. Стандарт реактивных хуков.</sub>
</div>
