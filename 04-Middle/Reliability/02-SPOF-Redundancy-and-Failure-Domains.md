# SPOF, redundancy и failure domains

## Цель

Находить общую точку отказа и размещать replicas так, чтобы redundancy переживала реальный failure domain.

## Prerequisites

Virtualization, Kubernetes scheduling, storage, networking и HA concepts.

## In scope

Failure domain — компонент, отказ которого затрагивает группу: process, node, rack, zone, power, network, storage или control plane. Две replicas на одном host не переживают host failure. Redundancy должна включать traffic, data, credentials, monitoring и recovery path.

## Практика

Нарисуйте dependency/failure map web + Kubernetes + PostgreSQL, найдите три SPOF и предложите controls с их стоимостью.

## Следующие темы

`03-Health-Checks-Graceful-Degradation-and-Failover.md`, HA architecture.
