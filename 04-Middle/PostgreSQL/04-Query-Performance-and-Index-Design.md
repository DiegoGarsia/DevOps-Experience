# Query performance и index design

## Цель

Оптимизировать запрос по измеренному plan и workload, а не по одному правилу о типе индекса.

## Prerequisites

Planner/EXPLAIN, statistics, MVCC, storage и application access pattern.

## In scope

Index design зависит от predicate, ordering, selectivity, write rate и размера таблицы. Composite index должен соответствовать ведущим колонкам; partial/expression/covering indexes решают специальные cases, но увеличивают complexity и maintenance.

## Практика

Соберите query workload, снимите baseline plan/latency, добавьте только необходимый index, измерьте read/write trade-off и проверьте regression.

## Следующие темы

`05-Table-Bloat-VACUUM-and-Maintenance.md`, capacity и troubleshooting.
