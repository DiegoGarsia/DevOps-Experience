# Установка и lifecycle Kubernetes-кластера

## Цель

Понять, из каких этапов состоит создание и обслуживание кластера, не сводя работу к одной команде установки.

## Prerequisites

Linux, networking, TLS/PKI, containers/CRI, storage, automation и control-plane architecture.

## In scope

Lifecycle включает подготовку OS, time sync, DNS, firewall, certificates, control plane, etcd, nodes, CNI, CSI, CoreDNS, RBAC, upgrades, backup и decommission.

Для lab допустим упрощённый дистрибутив, но production mental model должен включать version compatibility, API availability, quorum, node drain, workload disruption и rollback.

Upgrade выполняется по поддерживаемой последовательности: проверить compatibility и backup, обновить control plane, затем nodes с drain/uncordon и verification. Нельзя считать доступность API доказательством исправности workloads.

## Диагностика

Проверяйте cluster version, node conditions, control-plane health, etcd, CNI/CSI, certificates, events и базовый workload после каждой операции.

## Типичные ошибки

- установка без фиксирования версии и config;
- upgrade без etcd backup;
- drain без PDB/availability plan;
- забытый CNI/CSI;
- отсутствие recovery path при потере control plane.

## Практика

Разверните lab cluster, установите addon, добавьте node, выполните drain/uncordon и имитируйте failed upgrade в snapshot-able среде.

## Следующие темы

CI/CD integration, PostgreSQL, Observability и Middle Kubernetes operations.
