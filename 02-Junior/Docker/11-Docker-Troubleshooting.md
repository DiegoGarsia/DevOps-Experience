# Troubleshooting Docker

## Цель

Диагностировать контейнер по слоям, сохраняя факты до restart, remove или cleanup.

## Prerequisites

Docker architecture, image/build, lifecycle, storage, networking, registry и security.

## In scope

Порядок расследования:

1. `docker ps -a` и state/exit code;
2. `docker inspect` и effective configuration;
3. image digest, command и entrypoint;
4. logs/events и timestamps;
5. process, signal и resource limits;
6. mounts/volume и permissions;
7. network, DNS и published ports;
8. daemon/runtime/registry logs.

Типовые случаи:

- `Exited` — command, signal, dependency или OOM;
- `Restarting` — crash loop и неверная restart policy;
- `unhealthy` — health command, timeout или dependency;
- `connection refused` — listener/port/address;
- `permission denied` — UID/GID, mount, capability или SELinux;
- `pull denied` — registry authentication/authorization.

Не применяйте `prune`, не сохранив logs, inspect и image digest.

## Практика

Создайте failure lab с неверным entrypoint, volume permission, DNS alias и memory limit. Для каждого сценария оформите гипотезу, тест, исправление и verification.

## Следующие темы

CI/CD image pipeline, Kubernetes CRI и production container troubleshooting.
