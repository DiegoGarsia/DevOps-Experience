# Testing и linting Ansible

## Цель

Сделать automation проверяемой до запуска на production и отделить syntax quality от проверки реального desired state.

## Prerequisites

Roles, modules, idempotency, check mode, variables и execution strategies.

## In scope

Lint проверяет structure, naming, deprecated syntax, unsafe patterns и best practices. Syntax check подтверждает только parseability. Molecule или аналогичный test harness запускает ephemeral target и проверяет idempotency, convergence, ports, files и service behavior.

Минимальный CI pipeline для role:

```text
format/lint
  ↓
syntax-check
  ↓
check-mode
  ↓
apply in ephemeral host
  ↓
second apply must be idempotent
  ↓
verification
```

Тесты должны покрывать supported OS, privilege boundary, failure handling и secret redaction.

## Типичные ошибки

- считать syntax check тестом поведения;
- не запускать role второй раз;
- тестировать только happy path;
- применять test credentials к production;
- игнорировать warning lint.

## Практика

Подключите role к CI, добавьте lint, ephemeral VM/container, два apply и проверку service endpoint. Сломайте template и убедитесь, что pipeline останавливается до deployment.

## Следующие темы

Ansible JuniorPlus, CI/CD integration и Production troubleshooting.
