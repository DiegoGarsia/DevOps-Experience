# Table bloat, VACUUM и maintenance

## Цель

Связать MVCC churn, dead tuples, autovacuum, bloat и storage performance.

## Prerequisites

MVCC, VACUUM, indexes, WAL и long-running transactions.

## In scope

Bloat — неиспользуемое или плохо reclaimable пространство в table/index. Причины: update-heavy workload, удерживаемые snapshots, неудачный autovacuum tuning и недостаток maintenance window.

`VACUUM` и `ANALYZE` решают разные задачи; `VACUUM FULL` переписывает relation и требует сильного lock. Индексы могут bloat независимо от таблицы.

## Практика

Создайте update/delete churn, найдите long transaction, измерьте dead tuples/bloat и сравните обычный VACUUM с planned rewrite в test database.

## Следующие темы

`06-PostgreSQL-Configuration-and-Capacity.md`, PITR и performance architecture.
