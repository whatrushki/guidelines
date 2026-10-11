<div align="center">

<br />

# ДИЗАЙН-СИСТЕМА: ГОТОВЫЕ КОМПОНЕНТЫ И ЭФФЕКТЫ

Каталог готовых UI-компонентов из модуля `foundation-ui` и правила их применения.

<br />

<img src="https://img.shields.io/badge/library-foundation--ui-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/components-ready_to_use-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### Принцип повторного использования

> **ЗАПРЕТ НА БОЙЛЕРПЛЕЙТ:**  
> Все стандартные элементы ввода, поиска, переключателей, анимаций фона и экранов настроек уже реализованы в модуле `:foundation-ui`.  
> Категорически запрещено писать свои кастомные `TextField`, `SearchBar` или шейдерные фоны с нуля.

---

### Базовые компоненты ввода

#### 1. SearchBox (Строка поиска)
Имеет встроенную очистку, иконку лупы, скругление `shapes.large` и правильный цвет `surfaceContainerHigh`:
```kotlin
SearchBox(
    value = searchQuery,
    onValueChange = { searchQuery = it },
    placeholder = "Поиск по преподавателям или аудиториям...",
    modifier = Modifier.fillMaxWidth().padding(horizontal = 16.dp)
)
```

#### 2. StyledTextField (Стилизованное поле ввода)
Поддерживает кастомные суффиксы, префиксы, валидацию и плавный фокус:
```kotlin
StyledTextField(
    value = text,
    onValueChange = { text = it },
    placeholder = "Введите логин",
    leadingIcon = { Icon(WHATIcons.Person, contentDescription = null) }
)
```

#### 3. SegmentChoiceTab (Переключатель вкладок)
Используется для переключения дней недели, подгрупп или режимов просмотра:
```kotlin
SegmentChoiceTab(
    options = listOf("1 подгруппа", "2 подгруппа"),
    selectedIndex = selectedSubgroup,
    onSelect = { selectedSubgroup = it }
)
```

#### 4. Toggle (Фирменный свитчер)
Компактный переключатель состояния:
```kotlin
Toggle(
    checked = isEnabled,
    onCheckedChange = { isEnabled = it }
)
```

---

### Эффекты и фоновые анимации

#### 1. LiquidBackground (Анимированный градиент)
Используется на экранах онбординга, экрана авторизации, экрана ошибки или в шапке профиля. Выполняет плавное интерполирование слоев без нагрузки на CPU:
```kotlin
Box(modifier = Modifier.fillMaxSize()) {
    LiquidBackground() // Автоматически использует гармоничные цвета темы
    
    // Поверх рендерится контент
    Column(...) { ... }
}
```

#### 2. Shimmer (Скелетон загрузки)
Плавный эффект мерцания во время загрузки списков:
```kotlin
Box(
    modifier = Modifier
        .fillMaxWidth()
        .height(80.dp)
        .clip(MaterialTheme.shapes.large)
        .shimmer() // Добавляет мерцающий градиент
)
```

---

### Элементы экрана настроек

Вместо ручной верстки строк настроек с текстом и переключателями используйте семейство `*Setting`:
* **`SwitchSetting`** — строка настройки с переключателем.
* **`InputSetting`** — строка настройки с текстовым полем.
* **`MultiChoiceSetting`** — выбор одного из вариантов из выпадающего списка.
* **`SheetSetting`** — открытие модальной шторки для детальной настройки.

```kotlin
SwitchSetting(
    title = "Уведомления о заменах",
    description = "Получать пуш при изменении расписания",
    icon = WHATIcons.News,
    checked = isNotifyEnabled,
    onCheckedChange = { isNotifyEnabled = it }
)
```

<br />

<div align="center">
<sub>© WHAT Technologies. Каталог компонентов.</sub>
</div>
