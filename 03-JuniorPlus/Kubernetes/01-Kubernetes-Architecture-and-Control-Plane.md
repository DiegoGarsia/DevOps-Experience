# Архитектура Kubernetes и control plane

## Цель

Понять Kubernetes как распределённую систему управления desired state, а не как набор команд `kubectl`.

## Prerequisites

Linux processes, containers, OCI/CRI, networking, storage, Git и API fundamentals.

## In scope

Кластер состоит из control plane и worker nodes. API Server принимает declarative objects, etcd хранит состояние, scheduler назначает Pods на nodes, controller manager наблюдает desired/actual state и запускает reconciliation, kubelet управляет Pod на node.

```text
manifest
  ↓ API Server
etcd ← controllers ← scheduler
  ↓                    ↓
desired state       node assignment
                         ↓
                      kubelet → CRI → runtime
```

Control plane не запускает контейнеры напрямую. Node components получают назначение, создают Pod sandbox и поддерживают его состояние. CNI, CSI и CoreDNS являются отдельными расширениями cluster behavior.

## Основной принцип

Оператор описывает результат, а control loops постепенно приводят систему к нему. Временное расхождение между object и фактическим состоянием нормально; важно, что происходит reconciliation и есть ли сигнал о невозможности convergence.

## Диагностика

Начинайте с API availability, object status и events, затем переходите к controller/scheduler, node/kubelet, runtime и application.

## Типичные ошибки

- считать API Server scheduler;
- менять node вручную, не понимая controller reconciliation;
- принимать `kubectl apply` за успешный запуск приложения;
- не различать control plane и data plane.

## Практика

Нарисуйте поток Deployment от manifest до container process и укажите источник фактов на каждом переходе.

## Следующие темы

`02-API-Server.md`, `03-API-Objects-and-Desired-State.md` и etcd.
