# Roles и collections

## Цель

Разделять reusable automation, project-specific composition и versioned dependencies.

## Prerequisites

Playbooks, tasks, variables, templates, handlers и idempotency.

## In scope

Role имеет стандартные каталоги `tasks`, `handlers`, `templates`, `files`, `vars`, `defaults`, `meta` и `tests`. `defaults` предназначены для переопределяемых значений, `vars` — для более жёстких внутренних данных.

Collection объединяет roles, modules, plugins и документацию с namespace и version. Зависимости должны быть зафиксированы, проверены и получены из доверенного источника.

Role должна иметь узкий контракт: inputs, outputs/effects, supported platforms, privileges, handlers и verification. Скрытая модификация unrelated system state делает role опасной.

## Типичные ошибки

- хранить defaults в vars;
- role изменяет всё на host;
- не фиксировать collection version;
- конфликт имён variables;
- повторять role вместо композиции.

## Практика

Создайте role server baseline с defaults, template, handler и test, подключите её из playbook и зафиксируйте collection dependency.

## Следующие темы

`07-Idempotency-Check-Mode-and-Diff.md`, privilege escalation и testing.
