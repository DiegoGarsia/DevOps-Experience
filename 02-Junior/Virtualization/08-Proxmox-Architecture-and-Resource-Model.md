# Proxmox: архитектура и модель ресурсов

## Цель

Понять, как Proxmox объединяет KVM VM, LXC, storage, network и management API в operational platform.

## Prerequisites

Virtualization, KVM, virtual devices, storage и bridges.

## In scope

Proxmox VE предоставляет web UI, CLI и API поверх node services. VM использует KVM/QEMU, LXC — host kernel containers. Ресурсы описываются через VM/container configuration: CPU, memory, disks, NIC, boot order, agents и limits.

Кластерная модель добавляет nodes, shared configuration и quorum. HA не делает application автоматически resilient: для миграции нужны совместимые storage/network, а для stateful workload — корректная recovery strategy.

Automation должна работать через API или declarative tool, а не через ручные клики. Конфигурация VM, template, cloud-init и lifecycle policy должны быть versioned.

## Диагностика

Разделяйте проблему guest OS, QEMU/LXC, node resource, storage, bridge и cluster service. Проверяйте task log, node load, storage status, network path и guest agent.

## Типичные ошибки

- считать snapshot backup;
- выделять все CPU/RAM без capacity plan;
- размещать HA nodes в одном failure domain;
- менять VM configuration без записи;
- использовать shared storage как единственную копию данных.

## Практика

Создайте template VM, клонируйте её с cloud-init, задайте resource limits и проверьте lifecycle через API/CLI.

## Следующие темы

`09-Proxmox-Storage-Networking-and-HA.md`, Terraform provisioning и backup recovery.
