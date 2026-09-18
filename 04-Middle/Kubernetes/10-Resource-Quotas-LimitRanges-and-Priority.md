# ResourceQuota, LimitRange и Priority

## Цель

Ограничить blast radius namespace и предсказуемо управлять конкуренцией workloads за resources.

## Prerequisites

Requests/limits, namespaces, scheduler, QoS и autoscaling.

## In scope

ResourceQuota ограничивает aggregate requests, limits, object count и storage namespace. LimitRange задаёт defaults/min/max для отдельных Pods/containers. PriorityClass влияет на preemption при нехватке capacity.

Policy должна быть понятной: default limit без правильного request может неожиданно изменить QoS и scheduling. Preemption защищает critical workload, но может вызвать disruption и cascade.

## Диагностика

Проверяйте admission errors, quota usage/hard, LimitRange defaults, Pod priority и preemption events.

## Практика

Создайте два namespace с quota/limits, запустите workload до отказа, добавьте priority и оцените, какие Pods будут вытеснены.

## Следующие темы

`11-Admission-Controllers-and-Policy.md`, scheduling и production capacity.
