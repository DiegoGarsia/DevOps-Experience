# PostgreSQL: внутреннее устройство и диагностика

## Путь запроса

```text
application
↓ connection/authentication
↓ transaction
↓ parser/planner/executor
↓ index или sequential scan
↓ buffers/filesystem
↓ WAL/storage
```

PostgreSQL server принимает connections, planner строит plan по statistics, executor читает pages. MVCC хранит версии строк, поэтому readers и writers часто не блокируют друг друга напрямую. Vacuum удаляет obsolete versions, Analyze обновляет statistics.

## ACID и transaction

Atomicity — commit или rollback. Consistency — constraints/invariants. Isolation — видимость concurrent changes. Durability — committed data переживает crash благодаря WAL и flush policy. Уровень isolation влияет на anomalies и contention.

## Locks и MVCC

Row/table locks защищают конфликтующие операции. Долгая transaction удерживает старые версии, блокирует vacuum progress и может привести к bloat. `pg_stat_activity` и `pg_locks` дают картину waiters/blockers.

## WAL и replication

WAL — журнал изменений до data pages. Crash recovery replay'ит WAL. Streaming replication передаёт WAL на replica; lag возникает при сетевом, CPU, disk или apply bottleneck. Replication повышает availability/read scale, но не заменяет backup от логического удаления.

## Команды

```bash
pg_isready -h HOST -p 5432
psql -c 'select pid,usename,state,wait_event_type,query from pg_stat_activity;'
psql -c 'select * from pg_locks;'
psql -c 'select * from pg_stat_replication;'
psql -c 'select pg_size_pretty(pg_database_size(current_database()));'
psql -c 'select * from pg_stat_user_tables;'
psql -c 'EXPLAIN (ANALYZE, BUFFERS) SELECT ...;'
```

## Диагностика медленного запроса

```text
подтвердить latency и scope
↓
activity/wait/locks
↓
EXPLAIN ANALYZE BUFFERS
↓
statistics/index/selectivity/bloat
↓
CPU/cache/I/O/connections
↓
изменить один фактор
↓
повторить измерение
```

Не добавляй index или повышай `work_mem` без измерения: index удорожает writes, а `work_mem` может выделяться многократно на query/node.

## Вопросы

- **WAL?** Журнал, обеспечивающий crash recovery и replication.
- **MVCC?** Concurrent versions позволяют читателям видеть согласованный snapshot.
- **Replication и backup?** Replication ускоряет failover, backup восстанавливает после logical mistake.
- **Почему `max_connections` опасен?** Каждое connection потребляет memory и конкурирует за CPU/locks; pooling часто лучше бесконтрольного роста.

## Как объяснить за 30–60 секунд

«PostgreSQL обрабатывает query через connection, transaction, planner и executor, читая pages через buffers и записывая WAL. Для slow query я не начинаю с настройки: смотрю activity/locks, plan с buffers, statistics, bloat, I/O и connections. Backup и replication решают разные failure scenarios».

[[04-Middle/Reliability/Availability-Backup-Recovery]], [[05-Production-Troubleshooting/PostgreSQL]].

## От connection к storage

Connection process получает authentication и session settings. Transaction устанавливает snapshot и locks. Planner выбирает plan по statistics, executor читает buffers; при изменении rows создаются новые tuple versions и WAL records. Checkpoint сбрасывает dirty pages, а recovery replay'ит WAL после crash.

## Lock contention и deadlock

Lock contention — ожидание ресурса, который удерживает другой transaction. Deadlock — цикл ожиданий: T1 ждёт lock T2, T2 ждёт lock T1; PostgreSQL обнаруживает его и aborts одну transaction. Starvation — transaction долго не получает ресурс из-за постоянного приоритета других.

```sql
SELECT pid, state, wait_event_type, wait_event, query
FROM pg_stat_activity
WHERE datname = current_database();

SELECT blocked.pid AS blocked_pid, blocker.pid AS blocker_pid
FROM pg_stat_activity blocked
JOIN pg_stat_activity blocker
  ON blocker.pid = ANY(pg_blocking_pids(blocked.pid));
```

Не убивай blocker до проверки transaction age, owner и business impact.

## Backup и restore

`pg_dump` — logical backup, `pg_restore` восстанавливает custom format. Logical backup полезен для schema/data recovery, но не заменяет physical/WAL strategy для больших RPO/RTO требований.

```bash
pg_dump -Fc -d APP -f APP.dump
pg_restore --list APP.dump
createdb restore_test
pg_restore -d restore_test APP.dump
psql -d restore_test -c '\dt'
```

Успешное создание файла не гарантирует, что restore возможен: проверяй checksum, содержимое, credentials, extensions, permissions и application query.

## Практическое задание

1. Создай concurrent transactions и наблюдай locks/waits.
2. Получи deadlock двумя разными порядками update и объясни, какая transaction aborts.
3. Сравни sequential scan и index scan через `EXPLAIN (ANALYZE, BUFFERS)`.
4. Создай bloat, выполни VACUUM/ANALYZE и сравни статистику.
5. Сделай dump, повреди/неполностью скопируй его и проверь restore failure.
6. Проверь replication lag и поведение application при failover.
