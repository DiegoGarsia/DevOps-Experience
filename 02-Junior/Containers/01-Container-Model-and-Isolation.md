# Модель контейнера и изоляция

## Цель

Понять контейнер как обычный процесс Linux с ограниченными видимостью и ресурсами, а не как маленькую виртуальную машину.

## Prerequisites

Процессы, filesystem, users, network namespaces и cgroups на концептуальном уровне.

## In scope

Контейнер состоит из процесса или process tree, root filesystem, namespace configuration, cgroup limits, capabilities, environment и network/storage integration. Контейнеризация меняет то, что процесс видит и чем может управлять, но kernel остаётся kernel host.

```text
Application process
  ├── PID namespace
  ├── mount namespace
  ├── network namespace
  ├── user namespace
  └── cgroup membership
```

Image — immutable template filesystem layers, container — runtime instance. Изменения writable layer обычно эфемерны, поэтому состояние нужно хранить через volume или внешнюю систему.

Изоляция имеет уровни: namespace скрывает объект, cgroup ограничивает/учитывает ресурсы, capability уменьшает privilege, seccomp фильтрует syscalls, LSM добавляет policy. Ни один механизм отдельно не делает контейнер безопасным.

## Типичные ошибки

- считать контейнер VM;
- хранить database data только в writable layer;
- запускать процесс от root без необходимости;
- считать namespace security boundary для недоверенного kernel-level кода;
- не ограничивать resources.

## Практика

Сравните process на host и в контейнере по PID, mount tree, network interfaces и resource counters. Запишите, что скрывается, а что остаётся общим.

## Следующие темы

`02-Linux-Namespaces.md`, `03-cgroups-Resources-and-OOM.md` и OCI runtime.
