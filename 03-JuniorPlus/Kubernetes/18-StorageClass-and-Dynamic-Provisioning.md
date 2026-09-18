# StorageClass и dynamic provisioning

## Цель

Понять, как PVC автоматически создаёт storage и какие параметры class влияют на performance, topology и lifecycle.

## Prerequisites

PV/PVC, CSI concept, storage backend и scheduling.

## In scope

StorageClass задаёт provisioner, parameters, reclaimPolicy, volumeBindingMode и allowVolumeExpansion. Dynamic provisioning создаёт PV после запроса PVC. `WaitForFirstConsumer` позволяет учитывать topology и node placement до создания volume.

Provisioner/CSI controller взаимодействует с внешним storage API, а node plugin выполняет attach/mount на node. Ошибка может быть на любом участке.

## Диагностика

Смотрите PVC/PV events, StorageClass, CSI controller/node logs, backend quota и topology. Разделяйте provisioning, attach, mount и application permission failure.

## Типичные ошибки

- default StorageClass не соответствует workload;
- volume создан в неправильной zone;
- reclaimPolicy удаляет важные данные;
- expansion выполнен в backend, но не в filesystem;
- CSI credential имеет избыточные права.

## Практика

Создайте две StorageClass с разной policy, проверьте dynamic provisioning и намеренно сломайте CSI credentials, затем восстановите claim.

## Следующие темы

`19-Probes-and-Application-Health.md`, CSI operations и StatefulSets.
