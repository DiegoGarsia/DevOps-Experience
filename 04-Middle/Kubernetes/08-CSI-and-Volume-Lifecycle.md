# CSI и lifecycle volume

## Цель

Разобрать путь volume от PVC до attach/mount на node и диагностировать ошибки между Kubernetes и storage backend.

## Prerequisites

PV/PVC, StorageClass, CSI, nodes, mounts и storage performance.

## In scope

CSI разделяет controller и node plugins. Controller выполняет provisioning, attach и snapshot operations, node plugin — mount/stage/publish в kubelet path. Volume lifecycle включает create, bind, attach, mount, use, unmount, detach и delete.

Stateful recovery требует знать reclaim policy, topology, access mode, encryption, backup и consistency.

## Диагностика

Проверяйте PVC/PV events, external-provisioner/attacher logs, node plugin, cloud/storage API, device path, filesystem и permissions.

## Типичные ошибки

- volume создан не в той zone;
- detach блокирует старый node;
- mount успешно, но UID/GID неверен;
- delete policy удаляет данные;
- snapshot принят за backup.

## Практика

Создайте PVC, выполните attach/mount, отключите node, восстановите volume на другом node и проверьте data integrity.

## Следующие темы

`09-HPA-VPA-and-Autoscaling.md`, StatefulSets и DR.
