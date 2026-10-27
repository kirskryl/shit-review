# shit-review

Независимая проверка плана или готового бота/сервиса после «готово».

Запускает отдельного subagent (не того, кто писал код) и гоняет четыре команды по порядку: лучшее решение → несколько шагов вперёд → ошибки и безопасность → запуск и демо.

## Триггеры

`/shit-review`, «shit review», «проверь план», «проверь бота», «проверь сервис», «агент сказал готово».

Только по явной команде (`disable-model-invocation: true`).

## Установка

```bash
git clone https://github.com/kirskryl/shit-review.git ~/.cursor/skills/shit-review

# опционально — зеркало для Claude Code:
ln -sfn ~/.cursor/skills/shit-review ~/.claude/skills/shit-review
```

Файлы:
- [`SKILL.md`](SKILL.md) — протокол активации и запуска subagent
- [`agents/shit-review.md`](agents/shit-review.md) — инструкции для ревьюера

## Модели

| Раннер | Модель |
|--------|--------|
| Claude Code | `opus` (Opus 5, medium) |
| Cursor | `gpt-5.6-terra-medium`, fallback `kimi-k3-high` |

## Обновление

```bash
git -C ~/.cursor/skills/shit-review pull
```

## Лицензия

MIT — см. [LICENSE](LICENSE).
