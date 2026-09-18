# HPA, VPA и autoscaling

## Цель

Понимать, что autoscaling изменяет и какие сигналы/ограничения делают scaling безопасным.

## Prerequisites

Requests/limits, metrics, scheduler, workload controllers и capacity planning.

## In scope

HPA изменяет replicas по metrics, VPA предлагает или применяет resource requests, cluster autoscaler добавляет/удаляет nodes. Scaling не исправляет database bottleneck, lock contention или downstream saturation автоматически.

HPA требует корректных requests, metrics freshness, min/max replicas и stabilization window. Важно учитывать startup time, connection pools, queue depth и cost.

## Диагностика

Проверяйте metric availability, target calculation, desired/current replicas, pending Pods, node capacity и scale events. Distinguish no signal, no capacity и приложение не успевает scale.

## Практика

Создайте HPA для CPU или custom request metric, вызовите нагрузку, затем ограничьте node capacity и исследуйте разницу между desired replicas и фактическими.

## Следующие темы

`10-Resource-Quotas-LimitRanges-and-Priority.md`, performance и reliability.
