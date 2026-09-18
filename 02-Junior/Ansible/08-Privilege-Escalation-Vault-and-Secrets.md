# Privilege escalation, Vault и secrets

## Цель

Безопасно выполнять отдельные administrative tasks и не хранить секреты в открытом playbook или log.

## Prerequisites

Linux users/groups, SSH, permissions, Ansible variables и trust boundaries.

## In scope

`become` запускает task с повышенными правами, обычно через `sudo`. Повышение должно быть ограничено task/role и иметь audit. SSH user не обязан быть root; лучше использовать минимальные sudo rules.

Ansible Vault шифрует данные в repository, но ключ Vault остаётся чувствительным secret и должен доставляться через защищённый CI или operator workflow. Vault не заменяет rotation, owner и revocation policy.

`no_log: true` скрывает вывод task, но не исправляет утечку через application logs, command args, facts или artifact.

## Типичные ошибки

- `become: true` на весь play без необходимости;
- password/sudo token в inventory;
- Vault password в Git;
- `no_log` скрывает ошибку и усложняет диагностику;
- secret остаётся на remote host после task.

## Практика

Создайте роль, где только package/service tasks используют `become`, secret доставляется из защищённого источника, а временный файл удаляется после применения.

## Следующие темы

`09-Execution-Strategies-and-Delegation.md`, testing и secret lifecycle.
