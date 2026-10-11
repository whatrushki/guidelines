<div align="center">

<br />

# ДИЗАЙН-СИСТЕМА: МНОГОТЕМНОСТЬ И ЦВЕТОВАЯ ПАЛИТРА

Спецификация тем оформления, цветовых токенов и правил адаптации поверхностей в приложениях WHAT Technologies.

<br />

<img src="https://img.shields.io/badge/design-system-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/themes-multistyle-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/color_system-material_3_extended-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### Архитектура тем оформления

В приложениях WHAT Technologies реализована двухуровневая модель управления темами:
1. **Режим освещения (`ThemeType`)**: `Dark` (темный), `Light` (светлый), `System` (автоматически по системным настройкам устройства).
2. **Стиль оформления (`ThemeStyle`)**:
   * **`Brand` (Фирменный темно-синий — дефолт)**: глубина и контраст на базе акцентного цвета `#1F2137`.
   * **`Monochrome` (Монохром)**: бескомпромиссный минимализм, абсолютный OLED-черный `#000000` и строгие оттенки серого.
   * **`Material / Dynamic` (Material You)**: адаптация под системную палитру обоев (на Android 12+), с автоматическим fallback на Brand на Desktop/Web/iOS.
   * **`Plum` (Сливовый)**: изысканный глубокий фиолетовый оттенок с динамическим расчетом контейнеров.
   * **`CustomColor`**: возможность для пользователя задать произвольный акцентный цвет бренда, на базе которого библиотека `MaterialKolor` математически строит гармоничную 30+ цветовую схему.

---

### Спецификация стилей тем

#### 1. Monochrome (OLED & Минимализм)
Идеально выверенная контрастная схема. В темном режиме фон — абсолютный черный `#000000`.

| Токен | Dark Monochrome | Light Monochrome | Назначение |
| :--- | :--- | :--- | :--- |
| `primary` | `#FFFFFF` | `#000000` | Главные кнопки, ключевые акценты |
| `onPrimary` | `#000000` | `#FFFFFF` | Текст на главных кнопках |
| `primaryContainer` | `#262626` | `#E0E0E0` | Вторичные кнопки, плашки выбора |
| `background` | `#000000` | `#FFFFFF` | Основной фон всего экрана |
| `surface` | `#121212` | `#FAFAFA` | Поверхность карточек, диалогов |
| `surfaceContainerLow` | `#121212` | `#FAFAFA` | Низкоприоритетные подложки |
| `surfaceContainer` | `#1E1E1E` | `#F0F0F0` | Карточки расписания, списки |
| `surfaceContainerHigh`| `#222222` | `#EEEEEE` | Вложенные чипсы, поля ввода |
| `surfaceBright` | `#282828` | `#FFFFFF` | Подсвеченные активные карточки |
| `surfaceDim` | `#0E0E0E` | `#E8E8E8` | Затемненные фоновые шторки |
| `outline` | `#737373` | `#737373` | Активные границы |
| `outlineVariant` | `#404040` | `#CCCCCC` | Тонкие разделители между элементами |

#### 2. Brand Navy (Фирменный стиль `#1F2137`)
Базовый акцентный цвет:
* **Ключевой тон**: `#1F2137` (глубокий индиго/нави).
* Динамически генерирует через `getBrandColorScheme(Color(0xFF1F2137), isDark)` богатую гармоничную гамму, сохраняя глубокий темный фон (`#0D0E15`) и мягкие сине-серые контейнеры.

#### 3. Material Dynamic (Динамический Android 12+)
* **Android:** использует системные API `dynamicDarkColorScheme(context)` и `dynamicLightColorScheme(context)`.
* **Desktop / Web / iOS:** автоматически деградирует (Graceful Degradation) до стиля `Brand Navy` без крашей и артефактов.

---

### Правила использования семантических токенов в UI

> **СТРОГИЙ ЗАПРЕТ:**  
> Категорически запрещено жестко хардкодить цвета (`Color(0xFF...)`, `Color.White`, `Color.Black`, `Color.Gray`) внутри верстки компонентов!  
> Исключение составляют только фиксированные брендовые логотипы или кастомный фон `LiquidBackground`.

#### Стандартные привязки:
1. **Фон экранов:** Всегда `MaterialTheme.colorScheme.background` (или не указывать вовсе, если используется `Surface` / `Scaffold`).
2. **Карточки и контейнеры:**
   * Обычная карточка: `MaterialTheme.colorScheme.surfaceContainer` (или `surface`).
   * Вложенный элемент карточки: `MaterialTheme.colorScheme.surfaceContainerHigh`.
   * Поле поиска: `MaterialTheme.colorScheme.surfaceContainerHigh`.
3. **Текст:**
   * Основной текст: `MaterialTheme.colorScheme.onSurface`.
   * Вторичный/пояснительный текст: `MaterialTheme.colorScheme.onSurfaceVariant` (или `outline`).
   * Акцентный текст: `MaterialTheme.colorScheme.primary`.
4. **Разделители и обводки:**
   * Границы полей и тонкие линии: `MaterialTheme.colorScheme.outlineVariant`.

---

### Эталонный код применения темы в приложении

```kotlin
@Composable
fun AppTheme(
    settings: AppValues = koinInject(),
    content: @Composable () -> Unit
) {
    val themeType by settings.themeType.collect()
    val themeStyle by settings.themeStyle.collect()
    val themeColor by settings.themeColor.collect()
    val isSystemDark = isSystemInDarkTheme()
    
    val isDarkTheme = when (themeType) {
        ThemeType.Dark -> true
        ThemeType.Light -> false
        ThemeType.System, null -> isSystemDark
    }
    
    val colorScheme = when (themeStyle) {
        ThemeStyle.Monochrome -> if (isDarkTheme) DarkMonochromeScheme else LightMonochromeScheme
        ThemeStyle.CustomColor -> getBrandColorScheme(
            brandColor = themeColor?.let { Color(it) } ?: Color(0xFF1F2137), 
            isDark = isDarkTheme
        )
        // В общем коде (или через DI-провайдер):
        else -> getBrandColorScheme(Color(0xFF1F2137), isDarkTheme)
    }
    
    WHATTheme(
        theme = colorScheme,
        isDark = isDarkTheme,
        content = content
    )
}
```

<br />

<div align="center">
<sub>© WHAT Technologies. Стандарт дизайн-системы.</sub>
</div>
