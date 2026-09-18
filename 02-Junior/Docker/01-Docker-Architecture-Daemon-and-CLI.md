# Docker: архитектура, daemon и CLI

## Цель

Понять, какие части Docker отвечают за API, image, lifecycle, network и storage, и не путать client с daemon.

## Prerequisites

Container model, namespaces, cgroups, OCI, runtime, storage и networking.

## In scope

Docker CLI отправляет запросы Docker Engine API. Daemon управляет images, containers, networks, volumes и обращается к container runtime. Registry является внешним хранилищем образов, а Compose — декларативным описанием нескольких сервисов.

```text
docker CLI
    ↓ API
Docker daemon
    ├── image store
    ├── container lifecycle
    ├── network/volume drivers
    └── runtime
```

Разделяйте context: CLI может обращаться к локальному или удалённому daemon. Доступ к Docker socket фактически даёт высокие права на host, поэтому его нельзя бездумно монтировать в контейнер.

## Диагностика

```bash
docker version
docker info
docker context ls
docker system df
```

Если CLI работает, но container не стартует, это не доказывает исправность daemon, runtime, storage или image.

## Типичные ошибки

- считать Docker CLI самим runtime;
- публиковать daemon socket наружу;
- не различать local и remote context;
- очищать `docker system prune` без оценки volumes/images;
- запускать privileged container для исправления любой ошибки.

## Практика

Создайте отдельный Docker context для test daemon, сравните `info` и lifecycle контейнера, затем отключите доступ к daemon socket и объясните отказ.

## Следующие темы

`02-Images-Layers-Tags-and-Digests.md`, Dockerfile и container lifecycle.
