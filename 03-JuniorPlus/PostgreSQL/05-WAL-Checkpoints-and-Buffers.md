# WAL, checkpoints и buffers

## Цель

Понять, как PostgreSQL обеспечивает durability и почему checkpoint/I/O влияют на latency и recovery time.

## Prerequisites

Transactions, MVCC, filesystems, storage latency и memory.

## In scope

WAL записывает изменения до того, как dirty data pages должны быть записаны в relation files. При crash recovery PostgreSQL воспроизводит WAL, чтобы привести страницы к согласованному состоянию. `fsync` и `full_page_writes` влияют на durability и corruption risk.

Shared buffers кэшируют data pages. Checkpoint flushes dirty pages и создаёт recovery reference point. Слишком частые checkpoints увеличивают write pressure, слишком редкие — объём WAL и recovery time.

## Диагностика

Смотрите WAL generation, checkpoint statistics, write latency, buffers, disk saturation и recovery logs. Связывайте database latency с storage, а не только с query plan.

## Типичные ошибки

- отключать `fsync` в production;
- считать checkpoint backup;
- выделять всю RAM shared buffers;
- игнорировать WAL disk capacity;
- менять checkpoint settings без измерения.

## Практика

Сгенерируйте write workload, наблюдайте checkpoints/WAL/I/O, затем сравните latency при разных settings в test environment.

## Следующие темы

`06-Indexes-Planner-and-EXPLAIN.md`, replication и backup.
