# CRI и интеграция runtime

## Цель

Увидеть границу между kubelet, CRI, containerd и low-level runtime и использовать её при troubleshooting.

## Prerequisites

OCI, containerd, runc, namespaces, cgroups и kubelet.

## In scope

CRI предоставляет kubelet API для Pod sandbox, containers, images, logs, exec и lifecycle. Runtime может иметь CRI plugin, который преобразует эти calls в containerd/runc operations.

Pod sandbox создаёт network context. Application containers Pod используют общие network namespace и могут взаимодействовать через localhost, но имеют отдельные processes/filesystems.

## Диагностика

При `ContainerCreating` проверяйте image pull, sandbox, CNI, mounts и runtime. При `CrashLoopBackOff` runtime уже мог успешно создать процесс; ищите exit code и application logs.

## Типичные ошибки

- путать OCI image с CRI API;
- искать все проблемы Pod через Docker CLI;
- удалять runtime data без backup diagnostics;
- менять CRI endpoint без согласования kubelet.

## Практика

Сопоставьте один Pod с kubelet log, CRI/runtime event, sandbox и process host. Сломайте pull и network отдельно.

## Следующие темы

`09-Manifests-Metadata-and-Namespaces.md`, Pods и Kubernetes troubleshooting.
