# Troubleshooting виртуализации

## Цель

Научиться разделять guest, hypervisor, node, storage и network layers при расследовании проблемы VM или LXC.

## Prerequisites

Все предыдущие темы раздела Virtualization.

## In scope

Начинайте с симптома и scope: одна VM, один node, один storage или весь cluster. Затем собирайте факты:

```text
Guest OS
  ↓
Virtual device
  ↓
Hypervisor process
  ↓
Node resources
  ↓
Storage/network backend
```

Типовые симптомы:

- VM не стартует — проверяйте config, disk, lock, node resources и task log;
- высокая latency — отделяйте guest I/O, host storage и CPU steal;
- нет сети — проверяйте vNIC, bridge, VLAN, guest IP, route и firewall;
- HA не выполняется — проверяйте quorum, fencing, shared resources и capacity;
- restore не работает — проверяйте backup integrity, metadata и network identity.

Не выполняйте destructive reset до сохранения logs и конфигурации. Snapshot rollback может удалить данные, созданные после точки snapshot.

## Практика

Создайте четыре controlled failure: выключенный bridge, заполненный storage, отсутствующий disk и потеря cluster network. Для каждого оформите symptom, facts, hypothesis, fix, verification и prevention.

## Следующие темы

Containers, Docker, Terraform и Ansible.
