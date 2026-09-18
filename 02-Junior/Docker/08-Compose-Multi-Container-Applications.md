# Compose и multi-container application

## Цель

Научиться описывать локальный или test stack декларативно, явно задавая сети, volumes, configuration и зависимости запуска.

## Prerequisites

Docker images, lifecycle, volumes, bridge networking, DNS и environment.

## In scope

Compose file описывает services, image/build, ports, networks, volumes, environment, healthcheck и resource settings. Имена service становятся DNS names внутри network. `depends_on` управляет порядком создания/запуска, но не гарантирует готовность приложения без health condition и retry в самом приложении.

```yaml
services:
  api:
    build: .
    ports: ["8080:8080"]
    depends_on:
      db:
        condition: service_healthy
    networks: [front, back]
  db:
    image: postgres:16
    volumes: [db-data:/var/lib/postgresql/data]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app"]
    networks: [back]
networks: {front: {}, back: {}}
volumes: {db-data: {}}
```

Compose подходит для локального и небольшого test environment, но не заменяет cluster scheduler, production rollout и HA design.

## Диагностика

Сначала проверяйте rendered configuration, затем состояние services, health, logs, network и volume.

## Типичные ошибки

- секреты в compose file;
- `depends_on` вместо readiness/retry;
- одинаковые host ports в нескольких проектах;
- anonymous volumes без lifecycle policy;
- использование Compose как production orchestrator без оценки.

## Практика

Соберите API + PostgreSQL stack, добавьте healthcheck, отдельные front/back networks, persistent volume и failure scenario с недоступной базой.

## Следующие темы

`09-Registries-Authentication-and-Pull-Behavior.md`, CI image pipeline и security.
