# PV и PVC

## Цель

Понять разделение между запросом workload на storage и фактическим persistent volume.

## Prerequisites

Block storage, mounts, containers, Stateful workload и backup.

## In scope

PVC — namespaced request на capacity, access modes и class. PV описывает или представляет выделенный storage resource. Binding связывает PVC с подходящим PV; Pod использует PVC через volume mount.

PVC lifecycle отделён от Pod lifecycle. `Retain`, `Delete` и `Recycle` policy по-разному определяют судьбу storage после удаления claim. Access mode не всегда означает возможность одновременной записи: это зависит от backend/CSI.

## Диагностика

Проверяйте PVC phase, events, PV binding, StorageClass, node topology, attach/mount errors и backend capacity. `Pending` claim не является проблемой приложения.

## Типичные ошибки

- удалить PVC без понимания reclaim policy;
- считать `ReadWriteOnce` universal database HA;
- использовать ephemeral volume для state;
- не проверять backup/restore;
- mismatch capacity/access mode.

## Практика

Создайте PVC, подключите его к Pod, удалите Pod и восстановите данные, затем проверьте поведение при удалении claim.

## Следующие темы

`18-StorageClass-and-Dynamic-Provisioning.md`, CSI и StatefulSets.
