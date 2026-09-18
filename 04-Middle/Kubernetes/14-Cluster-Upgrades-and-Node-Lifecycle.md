# Cluster upgrades и node lifecycle

## Цель

Проводить upgrade control plane и nodes с учётом compatibility, disruption, backup и rollback.

## Prerequisites

Cluster installation, etcd, kubelet/CRI, PDB, scheduling и observability.

## In scope

Перед upgrade проверяйте поддерживаемые версии, API deprecations, addon compatibility, etcd backup, capacity и recovery access. Node upgrade обычно включает cordon, drain, OS/runtime upgrade, kubelet upgrade, uncordon и workload verification.

Drain учитывает PDB, но не исправляет неподготовленный workload. Stateful volume, DaemonSet и local storage требуют отдельных исключений и recovery plan.

## Диагностика

Сопоставляйте node conditions, evictions, PDB violations, runtime, CNI/CSI и API errors. После каждого этапа проверяйте control plane, critical addons и application SLI.

## Типичные ошибки

- upgrade без etcd backup;
- drain всех nodes одного failure domain;
- пропущенные deprecated APIs;
- rollback бинарников без rollback data/schema;
- отсутствие maintenance communication.

## Практика

Обновите lab node по процедуре cordon/drain/upgrade/uncordon и проверьте recovery после намеренно прерванного этапа.

## Следующие темы

`15-Cluster-Observability-Events-and-Diagnostics.md`, security и production upgrades.
