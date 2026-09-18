# DaemonSets и node workloads

## Цель

Понять, когда workload должен существовать на каждом подходящем node и какие риски создаёт node-local state.

## Prerequisites

Pods, scheduler, tolerations, nodes, volumes и cluster observability.

## In scope

DaemonSet controller создаёт Pod на каждом node, соответствующем selector/affinity. Типовые задачи: log agent, node exporter, CNI/CSI plugin и security agent. Rolling update должен учитывать drain, readiness и возможность отсутствия агента на части nodes.

DaemonSet не означает один Pod на весь cluster: labels, taints и topology определяют scope. HostPath и privileged permissions увеличивают blast radius.

## Диагностика

Проверяйте desired/current number, node selector, tolerations, Pod conditions, image pull, host mounts и resource pressure.

## Типичные ошибки

- DaemonSet занимает весь host resource;
- агент не tolerates control-plane taint;
- privileged HostPath без threat model;
- rollout удаляет единственный node agent;
- node-local logs теряются при удалении node.

## Практика

Разверните node exporter как DaemonSet, ограничьте nodes labels, добавьте failure одного node и проверьте coverage метрик.

## Следующие темы

`03-StatefulSets-and-Stateful-Workloads.md`, cluster observability и security contexts.
