# Troubleshooting PostgreSQL

## Цель

Локализовать медленную, недоступную или теряющую данные database по слоям и сохранить evidence до вмешательства.

## Prerequisites

Все PostgreSQL foundation/operations topics, Linux, network и observability.

## In scope

Порядок: connections → locks/transactions → query plan → buffers/I/O → WAL/checkpoint → autovacuum/bloat → replication/lag → storage/capacity → backup/recovery.

Сначала зафиксируйте symptom, scope, timeline, active queries, waits, errors, resource metrics и recent changes. Terminate backend, failover или VACUUM FULL — actions с blast radius, их выполняют после гипотезы и backup/rollback assessment.

## Практика

Разберите сценарии connection exhaustion, deadlock, slow query, replication lag и disk pressure, каждый по цепочке facts → hypothesis → test → fix → verification.

## Следующие темы

Observability investigation, Production incidents и StrongMiddle PostgreSQL internals.
