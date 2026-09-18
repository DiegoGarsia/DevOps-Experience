# Reusable automation patterns

## Цель

Проектировать Ansible automation, которую можно безопасно применять к нескольким типам host и запускать повторно.

## Prerequisites

Roles, variables, handlers, idempotency, templates и testing.

## In scope

Используйте role contract, defaults, platform vars, tags для безопасных операций, handlers для reload и отдельные verification tasks. Общие операции выносите в role, environment-specific composition оставляйте в playbook.

## Практика

Соберите role baseline для двух distribution, добавьте conditional package name, service verification и второй idempotent запуск.

## Следующие темы

`02-Dynamic-Inventory-and-Scale.md`, CI и troubleshooting.
