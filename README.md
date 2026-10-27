# shit-review

Независимая проверка плана или готового бота/сервиса после «готово».

Запускает отдельного ревьюера на **самой сильной доступной** модели (не Astro, не Fable) и гоняет четыре команды: лучшее решение → несколько шагов вперёд → ошибки и безопасность → запуск и демо.

Работает в Codex / Cursor / Claude Code без привязки к конкретному IDE API.

## Триггеры

`/shit-review`, «shit review», «проверь план», «проверь бота», «проверь сервис», «агент сказал готово».

Только по явной команде (`disable-model-invocation: true`).

## Установка

### Codex

```bash
git clone https://github.com/kirskryl/shit-review.git ~/.codex/skills/shit-review
```

В `~/.codex/config.toml`:

```toml
[features]
skills = true
```

### Cursor / Claude

```bash
git clone https://github.com/kirskryl/shit-review.git ~/.cursor/skills/shit-review
ln -sfn ~/.cursor/skills/shit-review ~/.claude/skills/shit-review
```

Файлы:
- [`SKILL.md`](SKILL.md) — протокол активации
- [`agents/shit-review.md`](agents/shit-review.md) — инструкции для ревьюера

## Обновление

```bash
git -C ~/.codex/skills/shit-review pull
# или
git -C ~/.cursor/skills/shit-review pull
```

## Лицензия

MIT — см. [LICENSE](LICENSE).
