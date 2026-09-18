# Индексы, planner и `EXPLAIN`

## Цель

Понимать, как planner выбирает execution plan и как доказательно анализировать query performance.

## Prerequisites

SQL, tables, transactions, indexes, statistics и I/O.

## In scope

Planner оценивает варианты scan/join/sort по statistics и стоимости I/O/CPU. B-tree подходит для equality и range, но индекс должен соответствовать predicate и порядку columns. Индекс ускоряет чтение, но увеличивает storage, write cost и VACUUM work.

`EXPLAIN` показывает план, `EXPLAIN ANALYZE` реально выполняет запрос и добавляет actual timing/rows. Его применяйте осторожно к write queries и production load.

## Диагностика

Сравнивайте estimated rows с actual rows, scan type, loops, buffers, sort spill, planning/execution time и statistics freshness. Не оптимизируйте один query без workload context.

## Типичные ошибки

- создать индекс на каждую колонку;
- читать cost как миллисекунды;
- запускать `EXPLAIN ANALYZE` destructive query;
- игнорировать stale statistics;
- считать sequential scan всегда плохим.

## Практика

Создайте таблицу и workload, сравните plan до/после индекса, обновите statistics и объясните изменение actual rows.

## Следующие темы

`07-VACUUM-Autovacuum-and-Statistics.md`, query performance и replication.
