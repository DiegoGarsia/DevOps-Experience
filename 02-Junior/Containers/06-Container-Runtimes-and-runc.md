# Container runtimes и `runc`

## Цель

Понять границы между image management, container manager и low-level runtime.

## Prerequisites

Namespaces, cgroups, capabilities, OCI image layout и process lifecycle.

## In scope

Low-level runtime создаёт процесс по OCI bundle: config, root filesystem, namespaces, cgroups, capabilities, mounts и seccomp. `runc` — распространённый implementation этого уровня. Он не является registry, scheduler или полноценным image builder.

Более высокий runtime/container manager отвечает за pull image, unpack layers, network setup, volume lifecycle, logging и lifecycle API. Разделение слоёв позволяет Kubernetes использовать CRI-compatible runtime, а Docker — собственную более высокую модель.

```text
Image/registry
    ↓ unpack
Container manager
    ↓ OCI bundle
runc
    ↓
Linux kernel namespaces/cgroups
```

## Диагностика

Отделяйте ошибку image unpack от ошибки OCI config, kernel namespace, mount, cgroup или приложения. Проверяйте runtime events, stderr и созданные процессы.

## Типичные ошибки

- искать registry credentials в `runc`;
- считать runtime ответственным за orchestration;
- менять OCI config вручную в production;
- давать runtime privileged options без threat model.

## Практика

Создайте минимальный OCI bundle, запустите его через runtime в test namespace, измените command и capability, затем сопоставьте ошибку с уровнем конфигурации.

## Следующие темы

`07-containerd-and-CRI-Boundary.md`, lifecycle/storage и Docker architecture.
