# Backup, restore и recovery testing

## Цель

Доказать восстановимость системы регулярным test restore, а не наличием зелёного backup job.

## Prerequisites

RPO/RTO, storage, PostgreSQL/Kubernetes backup, secrets и networking.

## In scope

Recovery test проверяет integrity, dependencies, credentials, DNS/IP, application startup, data correctness и фактические RTO/RPO. Restore следует выполнять в изолированную среду с documented cleanup.

## Практика

Восстановите VM, database и Kubernetes manifest в отдельную среду, измерьте время, найдите отсутствующие prerequisites и обновите runbook.

## Следующие темы

`06-Capacity-Planning-and-Resource-Bottlenecks.md`, DR exercise и incident prevention.
