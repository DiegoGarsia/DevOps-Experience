# Lifecycle, resources и environment Docker-контейнера

## Цель

Научиться задавать предсказуемый lifecycle контейнера и понимать влияние CPU, memory, PID, user и environment settings.

## Prerequisites

Container lifecycle, cgroups, signals, images и environment variables.

## In scope

Команда `docker run` объединяет image, command, environment, mounts, network и resource policy. Container живёт, пока живёт его main process. Background process внутри контейнера не заменяет корректный PID 1 и может потерять signal handling.

Resource settings включают memory limit/reservation, CPU quota/weight, pids limit, file descriptor limit и restart policy. Restart policy не исправляет crash loop; она лишь задаёт реакцию daemon.

Environment передаётся при запуске и может содержать runtime configuration, но не должен быть единственным secret store. Immutable image должен быть одинаковым для сред, а environment/configuration — изменяться отдельно.

## Диагностика

```bash
docker inspect app
docker stats app
docker top app
docker events --since 10m
```

Сопоставляйте exit code, OOM flag, restart count, CPU throttling, mounts и фактический command.

## Типичные ошибки

- запускать приложение shell wrapper без `exec`;
- задавать memory limit без working-set estimate;
- передавать секрет через command line;
- использовать restart policy вместо alert;
- не задавать stop timeout для graceful shutdown.

## Практика

Запустите контейнер с заданными limits, отправьте `SIGTERM`, зафиксируйте graceful stop, затем создайте OOM и объясните event/log/exit code.

## Следующие темы

`06-Volumes-Bind-Mounts-and-Storage.md`, networking и Compose.
