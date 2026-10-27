---
name: shit-review
description: >-
  Независимая проверка плана работы или готового бота/сервиса после «готово».
  Автоматически запускает subagent shit-review: в Claude Code — на Opus 5 (medium);
  в Cursor — на GPT Terra (gpt-5.6-terra-medium), fallback Kimi K3 (kimi-k3-high).
  Четыре команды: лучшее решение, мышление наперёд, безопасность, запуск и демо.
  Вызывать по /shit-review, «shit review», «проверь план», «проверь бота», «проверь сервис»,
  «агент сказал готово».
disable-model-invocation: true
---

# shit-review

Агент закончил и говорит «готово». Дальше идут 4 команды, которые гоняют по очереди. Копируй как есть.

## Активация

Пользователь вызвал `/shit-review` или просит проверить план / бота / сервис после работы агента.

**Не делай проверку сам.** Сразу запусти subagent **shit-review** по таблице моделей ниже.

## Модель (обязательно)

Сначала определи раннер:

- **Claude Code** (Anthropic CLI; subagent через `Task`/`Agent` с `subagent_type`, модели `opus`/`sonnet`/`haiku`) → `model: opus` (Opus 5, reasoning medium). Fallback на Terra/Kimi не нужен.
- **Cursor** → таблица приоритетов ниже.

| Раннер | Приоритет | `model` slug | Когда |
|--------|-----------|--------------|--------|
| Claude Code | — | `opus` | всегда (Opus 5, medium) |
| Cursor | 1 (default) | `gpt-5.6-terra-medium` | всегда сначала |
| Cursor | 2 (fallback) | `kimi-k3-high` | если Terra не резолвится / Task вернул Invalid model |

Алгоритм (Cursor):
1. Запусти Task с `model: gpt-5.6-terra-medium`.
2. Если ошибка вида `Invalid model` / `could not be resolved` / модель отсутствует в allowlist — **один** retry с тем же prompt и `model: kimi-k3-high`.
3. В ответе пользователю укажи, на какой модели прошёл ревью (`Opus 5`, `Terra` или `Kimi K3`).
4. Не подменяй модель на другую без явной просьбы пользователя.

## Автозапуск (обязательно)

Запусти **ровно один** subagent через `Task`:

| Параметр | Значение |
|----------|----------|
| `description` | `shit-review` |
| `subagent_type` | `generalPurpose` |
| `model` | Claude Code: `opus` (Opus 5, medium). Cursor: `gpt-5.6-terra-medium` (fallback: `kimi-k3-high`) |
| `run_in_background` | `false` |

Перед запуском прочитай [`agents/shit-review.md`](agents/shit-review.md) и передай его логику в prompt subagent'а.

### Prompt для subagent

```text
Full Repository Path: <абсолютный путь к репо или workspace>
Режим: <План | Готовое>
Исходный запрос пользователя: <что просили>
Что сделано (со слов предыдущего агента): <кратко>
Diff: <branch changes | uncommitted changes | natural language>

Ты shit-review. Прочитай agents/shit-review.md из ~/.cursor/skills/shit-review/ и выполни все 4 команды по порядку.
Не защищай чужую работу. Если на шаге 4 не сходится с запросом — почини и покажи заново.
```

**Режим:**
- **План** — есть план/TZ, код ещё не писали или только начали
- **Готовое** — бот, сервис, фича уже реализованы

Если режим неясен — уточни у пользователя одним вопросом, затем запускай subagent.

## 4 команды (для справки; выполняет subagent)

1. «Убедись, что это лучшее решение этой задачи. Покажи, какие варианты ты рассмотрел и почему выбрал этот.»
2. «Убедись, что ты подумал на несколько шагов вперёд: что сломается, когда данных станет больше и когда придёт следующая задача поверх этой.»
3. «Убедись, что в коде нет ошибок и он безопасный. Проверь ключи, доступы и данные людей.»
4. «Запусти это и покажи, что работает. Не сходится с тем, что я просил, чини сам и покажи заново.»

**Важно.** Subagent (Opus 5 в Claude Code, Terra/Kimi K3 в Cursor) = отдельный ревьюер, не тот кто писал. Свою работу он защищает, чужую разбирает честно.

## После subagent

Кратко перескажи вердикт пользователю. Не переписывай весь отчёт — только статус, блокеры, next steps.

Если subagent упал на выбранной модели — один retry (в Cursor сначала Terra→Kimi по правилам выше, затем один повтор на уже выбранной; в Claude Code — один повтор на `opus`). Потом сообщи ошибку.

## Не путать

- **shit-review** — пост-проверка после «готово», всегда отдельным subagent (Claude Code — Opus 5 medium; Cursor — Terra, fallback Kimi K3)
- `/review-bugbot` — ревью diff перед merge
- `oracle` → `skills/review.md` — формальное ревью по ТЗ
