# Конфигурация и capacity PostgreSQL

## Цель

Связать database settings с workload, memory, CPU, storage, connections и recovery requirements.

## Prerequisites

Query performance, pooling, WAL, VACUUM, Linux resources и capacity planning.

## In scope

Настройки memory/work_mem, shared_buffers, connections, WAL/checkpoint, autovacuum и parallelism нельзя выбирать независимо. Capacity model учитывает peak concurrency, connection pool, replication, backup, maintenance и failover headroom.

## Практика

Составьте capacity model, измерьте baseline, измените один параметр в test workload и сравните latency, I/O, memory и recovery signals.

## Следующие темы

`07-PITR-and-Backup-Validation.md`, troubleshooting и HA.
