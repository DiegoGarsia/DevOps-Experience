# VACUUM, autovacuum и statistics

## Цель

Понять, зачем PostgreSQL удаляет старые tuple versions и как maintenance влияет на bloat, planner и storage.

## Prerequisites

MVCC, transactions, indexes, planner и table storage.

## In scope

MVCC оставляет старые версии строк, пока snapshots могут их видеть. VACUUM помечает пространство reusable, обновляет visibility information и предотвращает transaction ID wraparound. `VACUUM FULL` переписывает таблицу и требует больше lock/space.

Autovacuum запускается по thresholds и scale factors, отдельно для vacuum и analyze. Statistics нужны planner для оценки selectivity; после большой загрузки или skew их нужно обновлять.

## Диагностика

Проверяйте dead tuples, last vacuum/analyze, autovacuum activity, long transactions, table/index bloat и disk growth. Долгая transaction может блокировать cleanup, даже если autovacuum включён.

## Типичные ошибки

- отключить autovacuum;
- использовать VACUUM FULL как регулярный cron;
- не учитывать long-running snapshots;
- увеличить maintenance thresholds без capacity;
- считать analyze cleanup старых tuple.

## Практика

Создайте update/delete workload, наблюдайте dead tuples и autovacuum, откройте долгую transaction и объясните задержку cleanup.

## Следующие темы

`08-Backup-Restore-and-Operational-Basics.md`, replication и maintenance planning.
