# Modules, tasks и playbooks

## Цель

Научиться строить playbook из маленьких проверяемых задач, которые описывают состояние, а не последовательность ручных команд.

## Prerequisites

Inventory, SSH, YAML, Linux package/service model и idempotency concept.

## In scope

Playbook содержит plays, target hosts и tasks. Task вызывает module с параметрами и получает result: changed, failed, skipped, stdout, stderr и факты. Module предпочтительнее `shell`, потому что знает состояние ресурса и возвращает структурированный результат.

```yaml
- name: Prepare web host
  hosts: web
  become: true
  tasks:
    - name: Install nginx
      ansible.builtin.package:
        name: nginx
        state: present
    - name: Ensure service is enabled
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

Порядок tasks читаем, результат каждой операции проверяем, destructive action ограничиваем tags/confirmation.

## Типичные ошибки

- использовать shell для package/service;
- task делает несколько несвязанных действий;
- секрет в plain YAML;
- отсутствие `become` boundary;
- playbook успешен, но не проверяет application state.

## Практика

Напишите playbook для установки и запуска web service, затем запустите его дважды и зафиксируйте разницу `changed`/`ok`.

## Следующие темы

`03-Variables-Facts-and-Precedence.md`, templates и handlers.
