<div align="center">

<br />

# ДИЗАЙН-СИСТЕМА: ИКОНКИ WHATICONS

Каталог векторных иконок из модуля `foundation-ui` и правила их использования.

<br />

<img src="https://img.shields.io/badge/icons-WHATIcons-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/style-filled_vectors-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### Правило единства иконографии

В приложениях WHAT Technologies используется гармоничное сочетание двух проверенных библиотек иконок:

1. **Material Icons (`androidx.compose.material.icons:material-icons-extended`) — СТАНДАРТ ДЛЯ UI ДЕЙСТВИЙ:**
   * Рекомендуется и активно используется для стандартных системных действий: навигация (`ArrowBack`, `Close`), управление (`Check`, `Delete`, `Edit`, `Search`), меню (`MoreVert`, `Settings`, `Refresh`).
2. **WHATIcons (`app.what.foundation.ui.icons.*`) — СПЕЦИАЛИЗИРОВАННЫЙ БРЕНДОВЫЙ НАБОР:**
   * Используется для предметной области и доменных сущностей приложений (университеты, новости, терминал, логи аудитора, социальные сети, обновление APK).
   * Все иконки `WHATIcons` отрисованы в едином залитом стиле (`filled`) и оптимизированы в векторный формат Kotlin (`ImageVector`).

> **СТРОГИЙ ЗАПРЕТ:**  
> Запрещено подключать и использовать случайные сторонние иконочные библиотеки (FontAwesome, Feather, Bootstrap Icons) или неоптимизированные разнородные SVG с конфликтующей толщиной обводок.

---

### Каталог основных иконок

| Иконка | Название в коде | Назначение |
| :--- | :--- | :--- |
| 🏛️ | `WHATIcons.Building` | Университеты, колледжи, факультеты, корпуса |
| 📰 | `WHATIcons.News` | Новости, статьи, объявления |
| ✨ | `WHATIcons.Features` | Новые функции, фичи, онбординг |
| 📋 | `WHATIcons.Logs` | Журнал логов, консоль аудитора |
| 🌐 | `WHATIcons.Network` | Сеть, статус подключения, веб-сервисы |
| 💻 | `WHATIcons.Terminal` | Режим разработчика, системные команды |
| 🔄 | `WHATIcons.CloudSync` | Синхронизация расписания, обновление кэша |
| 🚪 | `WHATIcons.Room` | Аудитории, кабинеты, локации |
| 👤 | `WHATIcons.Person` | Профиль, преподаватель, студент |
| 👥 | `WHATIcons.Group` | Студенческие группы, подгруппы |
| 📦 | `WHATIcons.ApkInstall` | Обновление приложения, установка APK |
| 🔗 | `WHATIcons.Share` | Поделиться расписанием, экспорт ссылки |
| 💬 | `WHATIcons.Support` | Поддержка, обратная связь |
| 📑 | `WHATIcons.Copy` | Копирование в буфер обмена |
| 👑 | `WHATIcons.Crown` | Староста группы, премиум/особый статус |
| ▶️ | `WHATIcons.Run` | Запуск, переход к паре |
| 💬 | `WHATIcons.Quote` | Цитаты, цитирование в новостях |
| 🐛 | `WHATIcons.FrameBug` | Отчет о сбое, баг-репорт, экран ошибки |
| ✈️ | `WHATIcons.Telegram` | Ссылка на Telegram канал/чат |
| 🔵 | `WHATIcons.VK` | Ссылка на сообщество ВКонтакте |
| 🟢 | `WHATIcons.Whatsapp` | Мессенджер WhatsApp |

---

### Пример использования

```kotlin
import app.what.foundation.ui.icons.WHATIcons
import app.what.foundation.ui.icons.filled.Building
import app.what.foundation.ui.icons.filled.CloudSync

@Composable
fun UniversityCard(name: String, onSync: () -> Unit) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .clip(MaterialTheme.shapes.large)
            .background(MaterialTheme.colorScheme.surfaceContainer)
            .padding(16.dp),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Icon(
            imageVector = WHATIcons.Building,
            contentDescription = "Университет",
            tint = MaterialTheme.colorScheme.primary
        )
        
        Text(
            text = name,
            modifier = Modifier.weight(1f).padding(horizontal = 12.dp),
            style = MaterialTheme.typography.bodyLarge
        )
        
        IconButton(onClick = onSync) {
            Icon(
                imageVector = WHATIcons.CloudSync,
                contentDescription = "Синхронизировать",
                tint = MaterialTheme.colorScheme.onSurfaceVariant
            )
        }
    }
}
```

<br />

<div align="center">
<sub>© WHAT Technologies. Каталог иконок.</sub>
</div>
