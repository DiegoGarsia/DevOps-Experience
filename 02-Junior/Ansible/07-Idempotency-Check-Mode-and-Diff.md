# Idempotency, check mode и diff

## Цель

Понимать, почему повторный запуск должен стабилизировать состояние, и безопасно просматривать изменение до применения.

## Prerequisites

Modules, tasks, roles, handlers и desired-state model.

## In scope

Idempotent task при уже достигнутом состоянии возвращает `ok`, а не меняет систему снова. Это зависит от корректного module behavior, сравнения параметров и отсутствия hidden side effects.

`--check` оценивает предполагаемое изменение, `--diff` показывает различия для поддерживаемых modules. Check mode не гарантирует, что произвольный shell script безопасно симулируется.

```bash
ansible-playbook site.yml --check --diff
ansible-playbook site.yml --start-at-task 'Install package'
```

Destructive operation должна иметь explicit guard, backup и verification. `changed_when` и `failed_when` должны отражать реальное состояние, а не скрывать неудобный результат.

## Типичные ошибки

- shell task всегда возвращает changed;
- `changed_when: false` скрывает изменение;
- check mode принят за полную dry-run гарантию;
- diff раскрывает secret;
- повторный запуск меняет timestamp или перезапускает сервис.

## Практика

Запустите role три раза: обычный режим, check mode и после ручного drift. Объясните каждый changed/ok и исправьте неидемпотентную task.

## Следующие темы

`08-Privilege-Escalation-Vault-and-Secrets.md`, execution strategies и testing.
