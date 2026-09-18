# cgroups, ресурсы и OOM

## Цель

Понять, как cgroups учитывают и ограничивают CPU, memory, processes и I/O и почему контейнер может получить OOM до host.

## Prerequisites

Процессы, memory pressure, `/sys` и namespaces.

## In scope

cgroup организует процессы в иерархию и применяет controllers. CPU controller задаёт quota/weight, memory controller учитывает usage, reclaim и limit, pids controller ограничивает количество процессов, I/O controller влияет на доступ к storage.

Limit — верхняя граница, request или weight — относительный приоритет. Container memory limit создаёт отдельный failure domain: kernel может завершить process внутри cgroup, когда общий host ещё имеет свободную RAM.

В cgroup v2 параметры и события доступны в `/sys/fs/cgroup`. `memory.current`, `memory.max`, `memory.events`, `cpu.max` и `pids.current` помогают сопоставить ограничение с поведением.

## Диагностика

```bash
cat /sys/fs/cgroup/memory.current
cat /sys/fs/cgroup/memory.events
cat /sys/fs/cgroup/cpu.stat
```

Сравнивайте application RSS, cgroup current/limit, reclaim, throttling и kernel logs. CPU throttling проявляется как latency и низкий фактический CPU при достигнутой quota.

## Типичные ошибки

- назначить memory limit ниже рабочего набора;
- считать CPU limit гарантированным количеством ядер;
- игнорировать pids limit;
- смотреть только host metrics;
- лечить cgroup OOM увеличением лимита без поиска leak или cache.

## Практика

Запустите memory и CPU workload в cgroup, задайте limits, наблюдайте `memory.events` и throttling, затем сравните результат с host-level metrics.

## Следующие темы

`04-Capabilities-Seccomp-and-Rootless.md`, OCI images и container runtime.
