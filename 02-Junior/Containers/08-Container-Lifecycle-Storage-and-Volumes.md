# Lifecycle, storage и volumes контейнера

## Цель

Понять lifecycle контейнера и выбрать способ хранения данных, который соответствует нужной durability.

## Prerequisites

OCI runtime, filesystems, mounts, namespaces и cgroups.

## In scope

Контейнер проходит create, start, running, stop, kill, restart и remove. Stop обычно отправляет signal процессу PID 1 и ждёт timeout. Если приложение не обрабатывает сигнал, runtime применяет kill.

Writable layer удобна для временных изменений, но исчезает при remove. Bind mount использует путь host, named volume управляется container manager, tmpfs хранит данные в memory. Для stateful service необходимо определить ownership, backup, permissions и recovery.

```text
image layers + writable layer
        + volume/bind mount
        ↓
container root filesystem
```

Volume не делает данные автоматически replicated или backed up. Backup должен понимать consistency приложения и права владельца.

## Диагностика

Проверяйте container state, exit code, signal, mount list, owner и свободное место host. Отличайте данные в volume от данных writable layer.

## Типичные ошибки

- хранить database data только в container layer;
- монтировать host directory с неправильным UID/GID;
- удалять volume вместе с контейнером;
- считать volume backup без restore test;
- не дать приложению graceful shutdown.

## Практика

Запустите stateful test service с named volume, удалите контейнер, восстановите его с тем же volume, затем смоделируйте permission и disk-full failure.

## Следующие темы

`09-Container-Networking.md`, Docker volumes и Kubernetes PV/PVC.
