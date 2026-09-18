# Workspaces, testing и troubleshooting Terraform

## Цель

Разделять environments, проверять module behavior и диагностировать Terraform по слоям.

## Prerequisites

State, modules, remote backend, import и drift.

## In scope

Workspace — отдельный state context для одной configuration. Он удобен для похожих ephemeral environments, но не заменяет явное разделение backend/project, когда нужны разные credentials и security boundaries.

Проверки включают formatting, validation, provider lock, plan review, static analysis, module tests и policy checks. Test должен проверять контракт, а не только отсутствие syntax error.

Порядок troubleshooting:

```text
configuration
  ↓ init/provider
variables and data
  ↓
backend/state/lock
  ↓
refresh/API permissions
  ↓
dependency graph/plan
  ↓
apply/result
```

## Типичные ошибки

- workspace как security isolation;
- тесты применяют production backend;
- план не сохраняется для review;
- provider error принимается за resource error;
- повторный apply без понимания частичного результата.

## Практика

Создайте два test workspace, добавьте format/validate/plan checks, сломайте provider credentials и state lock, затем восстановите pipeline без удаления state.

## Следующие темы

Ansible, CI integration и Terraform troubleshooting уровня Production.
