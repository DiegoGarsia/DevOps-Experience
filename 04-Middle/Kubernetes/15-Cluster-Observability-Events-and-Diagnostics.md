# Cluster observability, events и diagnostics

## Цель

Системно читать cluster signals и связывать object events с node/runtime/network/storage facts.

## Prerequisites

Kubernetes architecture, events, kubelet, CNI/CSI, Prometheus и logs.

## In scope

Сигналы Kubernetes: object status/conditions, Events, controller/scheduler logs, kubelet/runtime logs, node metrics, API audit и application telemetry. Event — короткий контекст, не долговременный incident record; его retention ограничен.

Диагностика начинается с scope: cluster, namespace, node, workload, Pod, container. Затем проверяйте control plane, scheduling, runtime, network, storage и application.

## Практика

Сгенерируйте Pending, ImagePull, readiness, eviction и volume mount failures, для каждого соберите evidence matrix и определите следующий слой.

## Следующие темы

`16-Security-Contexts-and-Pod-Isolation.md`, Observability operations и troubleshooting.
