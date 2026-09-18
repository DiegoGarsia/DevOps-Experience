# Requests, limits и QoS

## Цель

Понять, как requests влияют на scheduling, limits — на runtime throttling/OOM, а QoS — на eviction behavior.

## Prerequisites

CPU/memory, cgroups, scheduler, Pods и probes.

## In scope

Request — заявка для scheduler и capacity planning. Limit — runtime ceiling. CPU limit может вызвать throttling, memory limit — OOM kill внутри cgroup. QoS-класс зависит от requests/limits всех containers Pod и влияет на порядок eviction при pressure.

Requests должны отражать working set и burst, а не произвольное число. Слишком большие requests создают Pending Pods и снижают utilization; слишком маленькие дают contention и OOM.

## Диагностика

Сопоставляйте Pod spec, node allocatable, actual usage, throttling, memory events и eviction events. Не путайте container limit с total node capacity.

## Типичные ошибки

- ставить одинаковые CPU/memory limits всем;
- считать CPU limit гарантией latency;
- не задавать requests для critical workload;
- менять limits без наблюдения working set;
- лечить OOM только увеличением числа replicas.

## Практика

Создайте Pods с разными requests/limits, вызовите CPU throttling и memory pressure, определите QoS и порядок eviction.

## Следующие темы

`21-Rollout-and-Rollback.md`, scheduling и autoscaling.
