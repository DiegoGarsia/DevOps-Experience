# Linux namespaces

## Цель

Понять, какие категории объектов изолируют namespaces и почему разные namespaces вместе образуют container view.

## Prerequisites

Процессы, mounts, network interfaces и users.

## In scope

Основные namespaces:

- PID — видимость процессов и PID 1 внутри namespace;
- mount — собственное дерево mounts;
- network — interfaces, routes, ports и iptables context;
- UTS — hostname и domain name;
- IPC — shared memory, semaphores и queues;
- user — mapping UID/GID и rootless identity;
- cgroup — view cgroup hierarchy.

Процесс может находиться сразу в нескольких namespaces. `nsenter` позволяет исследовать namespace процесса с host, если есть права.

```bash
lsns -p "$PID"
readlink /proc/$PID/ns/pid
nsenter -t "$PID" -m -u -i -n -p sh
```

PID namespace имеет собственное numbering: процесс может иметь PID внутри контейнера и другой PID на host. PID 1 внутри namespace отвечает за reaping children и обработку сигналов; приложение, не рассчитанное на роль init, может оставлять zombies.

## Ограничения

Namespaces не виртуализируют kernel полностью. Kernel vulnerabilities, shared devices, privileged capabilities и неправильно настроенные mounts могут пересечь isolation boundary.

## Типичные ошибки

- исследовать namespace только по `ps` внутри контейнера;
- забывать network namespace при диагностике port;
- считать root внутри user namespace равным host root;
- использовать `nsenter` без понимания изменяемого контекста.

## Практика

Запустите process в отдельном PID и network namespace, сравните `/proc`, `ip addr`, hostname и process tree внутри и снаружи.

## Следующие темы

`03-cgroups-Resources-and-OOM.md`, capabilities и OCI runtime.
