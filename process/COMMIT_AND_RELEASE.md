<div align="center">

<br />

# РЕГЛАМЕНТ: ЧЕКЛИСТ КОММИТОВ И РЕЛИЗНЫЙ ЦИКЛ

Стандартизированный пошаговый процесс контроля качества перед фиксацией изменений и публикацией релизов.

<br />

<img src="https://img.shields.io/badge/process-continuous_delivery-09090b?style=for-the-badge&labelColor=000000" height="24" />
<img src="https://img.shields.io/badge/semver-strict-09090b?style=for-the-badge&labelColor=000000" height="24" />

<br />
</div>

---

### Чеклист перед каждым коммитом

Перед выполнением команды `git commit` разработчик или AI-ассистент обязан проверить следующие пункты:

1. **Компиляция всех таргетов:**
   * Проверить сборку локально: `./gradlew assembleDebug` (или таски конкретного модуля).
2. **Отсутствие платформенных утечек:**
   * Убедиться, что в `commonMain` не появились импорты `android.*`, `java.*` или `Context`.
   * Убедиться, что не использован `expect/actual` в фичах (только интерфейсы + Koin).
3. **Чистота верстки:**
   * Проверить скругления `shapes.large` и наличие модификатора `.clip(...)` у скролл-контейнеров.
   * Убедиться, что цвета привязаны к `MaterialTheme.colorScheme.*`, а не захардкожены.
4. **Хуки вместо бойлерплейта:**
   * Проверить отсутствие `remember { mutableStateOf(...) }` — заменено на `useState`.
5. **Git Status:**
   * Проверить `git status`: исключить случайные временные файлы, логи и артефакты сборки.

---

### Формат сообщений коммитов (Conventional Commits)

```
<тип>(<модуль/область>): <краткое описание на русском или английском>
```

* `feat(schedule): ...` — добавление новой функциональности.
* `fix(navigation): ...` — исправление бага.
* `refactor(theme): ...` — рефакторинг без изменения внешней логики.
* `docs(readme): ...` — изменение документации.
* `release: vX.Y.Z - ...` — коммит повышения версии и релиза.

---

### Регламент выпуска релиза (Release Workflow)

При подготовке мажорной, минорной или патч-версии соблюдается строгий порядок:

#### 1. Поднятие версии (Version Bump)
* В файле `gradle/libs.versions.toml` изменить:
  ```toml
  appVersion = "1.4.1" # или соответствующая версия
  ```

#### 2. Обновление Release Notes
Синхронизировать описание изменений в обоих файлах:
1. `metadata/release-notes.json`
2. `features/onboarding/src/commonMain/composeResources/files/release-notes.json`

Формат файла:
```json
{
  "version": "1.4.1",
  "version_code": 1410,
  "release_date": "2026-10-12",
  "title": "Что нового в v1.4.1",
  "changelog": [
    "Краткий пункт 1",
    "Краткий пункт 2"
  ]
}
```

#### 3. Коммит и тегирование
```bash
git add .
git commit -m "release: v1.4.1 - описание основных изменений"
git push origin master

# Создание и отправка тега:
git tag v1.4.1
git push origin v1.4.1
```

#### 4. Публикация GitHub Release
Создать релиз через GitHub CLI:
```bash
gh release create v1.4.1 --title "v1.4.1" --notes "Описание изменений..."
```

#### 5. Контроль CI/CD (GitHub Actions)
* Проверить статус запуска: `gh run list --limit 1`.
* Убедиться, что все джобы (Android APK/AAB, RuStore, Desktop, Web, iOS) завершились со статусом `success`.

<br />

<div align="center">
<sub>© WHAT Technologies. Регламент непрерывной интеграции.</sub>
</div>
