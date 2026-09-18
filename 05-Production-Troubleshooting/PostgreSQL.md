# PostgreSQL troubleshooting guide

## Доступность

`pg_isready` → service/listener → `ss -ltnp` → route/firewall → role/password/`pg_hba.conf`. Connection refused и authentication failure — разные ветви.

## Slow query/locks

```text
user latency
↓
pg_stat_activity/wait
↓
blocking PID/pg_locks
↓
EXPLAIN (ANALYZE, BUFFERS)
↓
statistics/index/bloat/I/O/connections
```

Не убивай blocker без понимания transaction и business impact. Низкий CPU не исключает lock или disk wait.

## Replication/failover

Проверь role, WAL/replication lag, network, disk и endpoint приложения. После failover старые pool connections могут продолжить обращаться к прежней ноде; нужен leader-aware endpoint и controlled reconnect.

## Backup/restore

Успешный exit code dump не доказывает recoverability. Проверь размер, checksum, список содержимого, restore в изолированную database, schema/data и user-path. Retention не должен удалять последнюю известную рабочую копию.

## Prevention

Metrics connections/locks/lag/WAL/disk, statement timeout, pooling, tested restore, independent backup и runbook failover.

[[05-Production-Troubleshooting/Universal-Method]], [[03-JuniorPlus/Databases/PostgreSQL-Internals]], [[04-Middle/Reliability/Availability-Backup-Recovery]].

## Сценарий: UPDATE ждёт, CPU низкий

`pg_stat_activity` показывает wait, `pg_blocking_pids` — blocker. Найди transaction age, owner и business impact. Не убивай session blindly: сначала проверь, можно ли завершить transaction, затем повтори запрос и проверь lock graph.

## Сценарий: backup job успешен, restore не работает

Проверь размер/sha256, `pg_restore --list`, версию client/server, extensions, users/permissions, свободное место и отдельный restore test. Exit code создания файла не доказывает бизнес-корректность данных.

## Prevention

Alert на connections, locks, replication lag, WAL/disk, backup age; регулярный restore в isolated environment; retention и независимое хранение.
