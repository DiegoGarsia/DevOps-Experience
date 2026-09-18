# Scheduling, affinity, taints и tolerations

## Цель

Научиться управлять размещением workload по topology, capacity и назначению node без скрытого нарушения availability.

## Prerequisites

Scheduler, requests/limits, labels, nodes и storage constraints.

## In scope

Node affinity выражает предпочтительные или обязательные labels. Pod anti-affinity распределяет replicas по nodes или zones. Taint отталкивает Pods, а toleration разрешает конкретному Pod находиться на node; toleration сама по себе не заставляет scheduler выбрать node.

Topology spread помогает распределять replicas по failure domains. Слишком жёсткие правила могут оставить Pod в Pending, а слишком мягкие — сконцентрировать replicas на одном failure domain.

## Диагностика

Сопоставляйте Pod constraints, node labels/taints, allocatable resources, topology и `FailedScheduling` events. Проверяйте, не противоречат ли affinity и anti-affinity друг другу.

## Типичные ошибки

- toleration без taint-aware design;
- required affinity на label, которого нет;
- anti-affinity без capacity;
- распределение только по hostname при отказе целой zone;
- изменение node labels без анализа уже запущенных Pods.

## Практика

Создайте три node labels и taint, разместите replicas с preferred/required rules, затем выключите одну zone и оцените availability.

## Следующие темы

`02-DaemonSets-and-Node-Workloads.md`, resource governance и failure domains.
