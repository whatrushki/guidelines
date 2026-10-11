<div align="center">

<br />

# АРХИТЕКТУРА: ХРАНИЛИЩЕ НАСТРОЕК И ШИФРОВАНИЕ

Реактивное типобезопасное хранилище настроек приложения с аппаратным шифрованием из модуля `foundation-preferences`.

<br />

<img src="https://img.shields.io/badge/library-foundation--preferences-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/encryption-AES--GCM_Keystore-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### Архитектура PreferenceStorage

Модуль `foundation-preferences` устраняет необходимость писать ручной boilerplate для сохранения настроек:
* **Типобезопасность**: работа через свойства-делегаты (`boolean`, `string`, `int`, `model<T>`).
* **Аппаратное шифрование**: флаг `isEncrypted = true` прозрачно использует криптографию AES-GCM с аппаратным хранением ключей в Android Keystore.
* **Реактивность в Compose**: прямое получение состояния через `storage::property.collect()`.
* **Реактивность в корутинах**: получение потока через `storage.property.observe(): Flow<T?>`.
* **Встроенное кэширование**: чтение значения не блокирует UI дисковым вводом-выводом.

---

### Эталонный класс настроек приложения (AppValues)

```kotlin
package app.what.schedule.data.local.settings

import app.what.foundation.data.settings.KeyValueStorage
import app.what.foundation.data.settings.PreferenceEncryptor
import app.what.foundation.data.settings.PreferenceStorage
import kotlinx.serialization.Serializable

@Serializable
enum class ThemeType { System, Dark, Light }

@Serializable
enum class ThemeStyle { Brand, Monochrome, Material, CustomColor }

@Serializable
data class UserAccount(val username: String = "", val token: String = "")

class AppValues(
    storage: KeyValueStorage,
    encryptor: PreferenceEncryptor? = null
) : PreferenceStorage(storage, encryptor) {

    // 1. Примитивные свойства:
    var isFirstLaunch by boolean("is_first_launch", defaultValue = true)
    var selectedGroup by string("selected_group", defaultValue = "")

    // 2. Сериализуемые модели и Enum:
    var themeType by model("theme_type", defaultValue = ThemeType.System)
    var themeStyle by model("theme_style", defaultValue = ThemeStyle.Brand)

    // 3. Зашифрованные конфиденциальные данные (AES-GCM):
    var authToken by string("auth_token", defaultValue = "", isEncrypted = true)
    var account by model("user_account", defaultValue = UserAccount(), isEncrypted = true)
}
```

---

### Регистрация в DI (Koin)

В платформенном модуле создается платформа-специфичное хранилище:

```kotlin
// androidMain:
val platformStorageModule = module {
    single<KeyValueStorage> { AndroidKeyValueStorage(androidContext()) }
    single<PreferenceEncryptor> { AndroidPreferenceEncryptor(androidContext()) }
    single { AppValues(get(), get()) }
}
```

---

### Использование в UI и контроллерах

#### В Composable:
```kotlin
@Composable
fun GroupSelector(settings: AppValues = koinInject()) {
    // Автоматическая рекомпозиция при изменении настройки:
    val group by settings::selectedGroup.collect()

    Text("Выбранная группа: ${group ?: "Не выбрана"}")

    Button(onClick = { settings.selectedGroup = "ИТ-21" }) {
        Text("Выбрать ИТ-21")
    }
}
```

#### В корутинах / контроллерах:
```kotlin
// Реакция на изменение токена в фоне:
settings.authToken.observe().collect { token ->
    if (!token.isNullOrBlank()) {
        syncUserData(token)
    }
}
```

<br />

<div align="center">
<sub>© WHAT Technologies. Стандарт хранилища данных.</sub>
</div>
