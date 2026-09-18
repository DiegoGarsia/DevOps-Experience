# StatefulSets и stateful workloads

## Цель

Понять, какие guarantees StatefulSet даёт identity и ordering, а какие обязанности остаются у database/application design.

## Prerequisites

Pods, PVC, Services, storage, replication и backup.

## In scope

StatefulSet создаёт стабильные имена Pod, network identity и volume claim template. Ordered startup/termination может быть нужен для quorum systems, но увеличивает время rollout и recovery.

StatefulSet не делает приложение replicated, consistent или backed up. Database operator/replication protocol должен управлять leader, member health, fencing и data recovery.

## Диагностика

Проверяйте Pod identity, PVC binding, ordinal, headless Service, readiness, replication state и storage latency. Разделяйте Kubernetes availability Pod и application data availability.

## Типичные ошибки

- удалить PVC вместе с StatefulSet без политики;
- использовать shared writable volume без backend support;
- rolling update несовместим с database schema;
- quorum потерян из-за ordered restart;
- считать stable identity заменой HA.

## Практика

Разверните stateful test service с отдельными PVC и headless Service, остановите member и проверьте identity, recovery и data integrity.

## Следующие темы

`04-Jobs-and-CronJobs.md`, CSI и PostgreSQL HA.
