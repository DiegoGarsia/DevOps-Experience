# Pods и Pod lifecycle

## Цель

Понять Pod как минимальную scheduling и network unit и отличать его lifecycle от lifecycle отдельного container.

## Prerequisites

CRI, kubelet, namespaces, cgroups, manifests и resources.

## In scope

Pod объединяет один или несколько containers с общими network namespace, IP, volumes и lifecycle. Обычно один Pod содержит один main application container; sidecar применяется, если тесная совместная lifecycle действительно нужна.

Фазы Pod включают Pending, Running, Succeeded, Failed и Unknown. Container states Waiting, Running и Terminated содержат reason/exit code. Restart policy Pod не равна rollout policy Deployment.

Init containers выполняются до основных containers. Probes влияют на readiness/liveness/startup, но не исправляют неверную configuration.

## Диагностика

Сначала смотрите Pod phase, conditions, container state, events, image, resources, mounts и logs предыдущего container. `Pending` и `CrashLoopBackOff` требуют разных гипотез.

## Типичные ошибки

- запускать несколько независимых приложений в одном Pod;
- использовать Pod IP как постоянный endpoint;
- считать restart успехом recovery;
- не задавать termination behavior;
- читать только последний log после restart.

## Практика

Создайте Pod с init container и sidecar, проверьте общую сеть/volume, намеренно завершите main container и исследуйте lifecycle.

## Следующие темы

`11-ReplicaSets-and-Deployments.md`, probes и Services.
