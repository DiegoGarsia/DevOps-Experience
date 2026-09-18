# Модель Ansible, inventory и connectivity

## Цель

Понять, как Ansible выбирает hosts, устанавливает соединение и выполняет desired state без agent на target host.

## Prerequisites

Linux users/permissions, SSH, YAML, Bash и configuration management model.

## In scope

Inventory описывает hosts и groups, variables — параметры подключения и состояния. Ansible controller запускает modules на target через SSH или другой connection plugin, получает structured result и удаляет временный код.

```ini
[web]
web-01 ansible_host=192.0.2.20

[web:vars]
ansible_user=deploy
ansible_ssh_private_key_file=~/.ssh/id_ed25519
```

Inventory может быть static или dynamic. Host key checking и privilege escalation должны быть явными. Controller не должен хранить production private keys без access control.

## Диагностика

Проверяйте `ansible-inventory --graph`, `--list`, `ansible all -m ping -vvv`, DNS/IP, SSH config, user, Python interpreter и sudo policy.

## Типичные ошибки

- hostname в inventory не резолвится;
- неверный user/key;
- ping module принят за проверку приложения;
- отключён host key checking без threat model;
- одна переменная смешивает адрес и credentials.

## Практика

Создайте inventory web/db групп, подключитесь отдельным SSH key, намеренно сломайте user и interpreter, диагностируйте через verbose output.

## Следующие темы

`02-Modules-Tasks-and-Playbooks.md`, variables и facts.
