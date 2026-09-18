# Docker volumes, bind mounts и storage

## Цель

Выбирать между writable layer, named volume, bind mount и tmpfs, понимая ownership, durability и backup consequences.

## Prerequisites

Filesystem, mounts, container lifecycle и permissions.

## In scope

Writable layer живёт вместе с container instance. Named volume управляется Docker и обычно хранится в data root daemon. Bind mount напрямую показывает контейнеру путь host. `tmpfs` хранит данные в памяти и исчезает после остановки.

```bash
docker volume create app-data
docker run --mount type=volume,src=app-data,dst=/var/lib/app app:1.0
docker run --mount type=bind,src=/srv/config,dst=/etc/app,ro app:1.0
```

Bind mount удобен для development и host-controlled configuration, но сильнее связывает контейнер с layout host. Volume лучше отделяет lifecycle приложения от host path, но требует отдельной backup policy. `:ro` уменьшает риск изменения host данных.

## Диагностика

Проверяйте `docker volume inspect`, mount propagation, UID/GID, filesystem space и фактический path data root. Не удаляйте volume до проверки, какой контейнер его использует.

## Типичные ошибки

- потерять данные при `docker rm -v`;
- дать контейнеру read-write bind mount без необходимости;
- mismatch UID/GID;
- считать volume реплицированным;
- backup volume во время неконсистентной записи базы.

## Практика

Запустите stateful контейнер с volume, уничтожьте container, восстановите данные, затем попробуйте read-only mount и диагностируйте ожидаемый отказ записи.

## Следующие темы

`07-Bridge-Networking-Ports-and-DNS.md`, registry и Compose.
