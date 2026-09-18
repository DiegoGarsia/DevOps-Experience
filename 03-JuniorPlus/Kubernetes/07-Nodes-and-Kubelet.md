# Nodes и kubelet

## Цель

Понять, как worker node принимает Pod assignment и поддерживает его фактическое состояние.

## Prerequisites

Linux, containers, CRI, scheduler и control-plane API.

## In scope

Node включает kubelet, container runtime, CNI, CSI и системные ресурсы. Kubelet получает PodSpec, создаёт sandbox и containers через CRI, монтирует volumes, запускает probes и публикует status/events.

Kubelet не является scheduler и не принимает решение о выборе node. Он отвечает за local convergence и сообщает о невозможности запуска. Node conditions, taints и capacity влияют на scheduling и eviction.

## Диагностика

Разделяйте node NotReady, kubelet failure, runtime failure, CNI failure, disk pressure, memory pressure и application failure. Сопоставляйте node conditions, kubelet logs, runtime events и Pod status.

## Типичные ошибки

- перезапускать kubelet при проблеме CNI;
- считать Ready доказательством исправности всех workloads;
- игнорировать disk/memory pressure;
- менять Pod на node вручную;
- не учитывать clock/certificate проблемы.

## Практика

Выведите node из Ready через controlled failure, наблюдайте Pod behavior, kubelet events и scheduler reaction, затем восстановите node.

## Следующие темы

`08-CRI-and-Runtime-Integration.md`, Pod lifecycle и resource behavior.
