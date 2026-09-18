# Proxmox storage, networking и HA

## Цель

Понимать зависимости HA-операции от quorum, storage, network и состояния guest.

## Prerequisites

Proxmox resource model, virtual networks, storage, backups и failure domains.

## In scope

Storage может быть local, shared filesystem, block storage или distributed backend. Для migration и HA важно, где находятся VM disks, насколько надёжен metadata path и сохраняются ли snapshots.

Cluster network переносит management, cluster communication и иногда storage traffic. Разделение VLAN/физических путей снижает конкуренцию и blast radius. Потеря corosync network может привести к quorum loss, даже если guest traffic работает.

HA manager может перезапустить VM на другом node, но не гарантирует zero downtime и application consistency. Защита от split-brain, quorum и fencing важнее простого количества nodes.

## Диагностика

Проверяйте cluster status, quorum, time synchronization, corosync latency, storage availability, node capacity и guest state. Не запускайте ручной failover, пока не понятна причина исходного отказа.

## Типичные ошибки

- кластер из nodes в одном физическом failure domain;
- shared storage без проверки latency и recovery;
- смешение storage и corosync traffic без capacity;
- ожидание HA при потере quorum;
- отсутствие fencing и recovery runbook.

## Практика

В lab-кластере отключите guest node network, затем storage path, сравните cluster state и определите, когда безопасен failover.

## Следующие темы

`10-Virtualization-Troubleshooting.md`, Terraform и Reliability failure domains.
