# Variables, facts и precedence

## Цель

Понимать источник effective value и не создавать конфигурацию, поведение которой зависит от случайного precedence.

## Prerequisites

Inventory, playbooks, YAML и structured configuration.

## In scope

Variables могут находиться в inventory, group_vars, host_vars, play, task, role defaults/vars и extra vars. Чем выше precedence, тем сильнее значение переопределяет другое; точную таблицу нужно держать рядом с проектом, а не полагаться на память.

Facts — сведения, собранные о target host: OS, interfaces, memory, packages и paths. Их сбор занимает время и может раскрывать чувствительные сведения, поэтому управляйте `gather_facts` и cache.

Имена variables должны описывать intent, а не implementation. Секретные values должны поступать через Vault/secret backend и иметь явную область действия.

## Диагностика

Используйте `debug` только для безопасных значений, `ansible-inventory --host`, `ansible_facts` и `-vvv`. Не печатайте secret variables и полный fact dump в CI.

## Типичные ошибки

- одинаковое имя variable в нескольких уровнях;
- host variable случайно переопределяет group policy;
- fact устарел после изменения host;
- `set_fact` создаёт скрытое состояние;
- secret попал в debug output.

## Практика

Задайте один параметр на нескольких уровнях, предскажите effective value, запустите playbook и затем удалите неоднозначные overrides.

## Следующие темы

`04-Templates-Jinja-and-Configuration.md`, handlers и roles.
