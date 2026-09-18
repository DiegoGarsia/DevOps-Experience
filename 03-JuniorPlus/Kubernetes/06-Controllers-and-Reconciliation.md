# Controllers и reconciliation

## Цель

Понять control loop Kubernetes: как controller наблюдает objects, вычисляет diff и приводит cluster к desired state.

## Prerequisites

API objects, watches, etcd, scheduler и Pod lifecycle.

## In scope

Controller получает events о desired и observed objects, сравнивает их и выполняет action. Deployment controller создаёт/обновляет ReplicaSet, ReplicaSet controller поддерживает число Pods, Job controller отслеживает завершение задач.

Reconciliation должен быть идемпотентным и устойчивым к повторным events, задержкам и конкурирующим изменениям. Eventual convergence не означает мгновенное выполнение.

## Диагностика

Проверяйте owner references, events, controller logs, generation/observedGeneration, child objects и resource conflicts. Если parent здоров, а child нет, ищите границу controller.

## Типичные ошибки

- редактировать child object вместо parent;
- ожидать один event на одно изменение;
- считать отсутствие event доказательством отсутствия reconciliation;
- создавать ручные objects с теми же labels/name.

## Практика

Измените replicas и image Deployment, наблюдайте цепочку parent → ReplicaSet → Pod и определите, на каком controller появляется каждый объект.

## Следующие темы

`07-Nodes-and-Kubelet.md`, Pods и Deployments.
