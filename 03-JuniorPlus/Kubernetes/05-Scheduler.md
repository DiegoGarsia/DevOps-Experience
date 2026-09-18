# Scheduler Kubernetes

## Цель

Понять, как scheduler выбирает node для Pod и почему Pending Pod требует анализа constraints, ресурсов и состояния node.

## Prerequisites

Pods, API objects, CPU/memory model и node/kubelet.

## In scope

Scheduler наблюдает unscheduled Pods, фильтрует nodes по constraints и feasibility, затем ранжирует допустимые nodes и записывает binding. Учитываются requests, taints/tolerations, affinity, topology, volume constraints и resource availability.

Scheduler не запускает container и не проверяет готовность приложения. После binding kubelet на выбранном node получает desired Pod и создаёт его через CRI.

## Диагностика

Ищите конкретную причину `FailedScheduling`: insufficient CPU/memory, taint, affinity conflict, volume zone или отсутствующие nodes. Сопоставляйте events с requests Pod и allocatable resources node.

## Типичные ошибки

- смотреть только на общий cluster capacity;
- путать requests с фактическим usage;
- добавлять toleration вместо проверки taint policy;
- считать `Pending` runtime failure;
- не учитывать topology и storage constraints.

## Практика

Создайте Pods с разными requests, taints и affinity, наблюдайте scheduler events и исправьте только одну constraint за раз.

## Следующие темы

`06-Controllers-and-Reconciliation.md`, metadata/labels и Middle scheduling.
