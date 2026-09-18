# containerd и граница CRI

## Цель

Понять, как kubelet обращается к runtime через CRI и где в цепочке находится containerd.

## Prerequisites

OCI, runc, namespaces, cgroups и Pod concept на базовом уровне.

## In scope

containerd управляет image lifecycle и container/task lifecycle, используя low-level runtime. Kubernetes не обязан знать детали `runc`; kubelet обращается к CRI runtime endpoint.

CRI разделяет операции:

- RuntimeService — sandbox, containers, start/stop, status, exec и logs;
- ImageService — pull, list, inspect и remove images.

Pod sandbox и container lifecycle не совпадают: network namespace обычно принадлежит sandbox, а application containers используют его. Поэтому один Pod может иметь несколько контейнеров в общей network context.

```text
Kubelet
  ↓ CRI gRPC
CRI plugin/runtime
  ↓
containerd
  ↓
runc + Linux kernel
```

## Диагностика

Проверяйте kubelet logs, CRI endpoint, runtime status, image pull, sandbox creation и конкретный container task. Ошибка `ImagePull` не равна ошибке запуска процесса.

## Типичные ошибки

- искать все container logs в Docker daemon;
- путать CRI с OCI;
- удалять sandbox при проблеме одного контейнера;
- диагностировать Kubernetes Pod только через runtime CLI;
- менять runtime endpoint без проверки kubelet configuration.

## Практика

Сопоставьте один Pod с kubelet event, CRI request, containerd task и процессом host. Остановите image registry и определите этап отказа.

## Следующие темы

`08-Container-Lifecycle-Storage-and-Volumes.md`, `09-Container-Networking.md` и Kubernetes kubelet.
