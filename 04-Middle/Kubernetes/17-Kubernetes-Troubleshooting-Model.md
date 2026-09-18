# Модель troubleshooting Kubernetes

## Цель

Диагностировать отказ по слоям и не исправлять symptom изменением случайного объекта.

## Prerequisites

Все Kubernetes topics JuniorPlus/Middle и общая troubleshooting method.

## In scope

```text
Symptom
  ↓ scope
API/object status/events
  ↓
controller/scheduler
  ↓
node/kubelet/runtime
  ↓
CNI/DNS/Service/Ingress
  ↓
CSI/storage
  ↓
application/database
```

`Pending`, `ImagePullBackOff`, `CrashLoopBackOff`, `NotReady`, `no endpoints`, `timeout` и `5xx` являются разными starting points. До restart/remove сохраняйте describe, events, logs previous, metrics, manifest и digest.

## Практика

Проведите end-to-end incident с недоступным сервисом: scope → facts → hypothesis → layer test → fix → verification → prevention.

## Следующие темы

PostgreSQL operations, Observability operations и Production troubleshooting.
