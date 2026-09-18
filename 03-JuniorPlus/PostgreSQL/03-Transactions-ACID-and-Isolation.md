# Transactions, ACID и isolation

## Цель

Понять границы атомарной операции и выбрать isolation level по требуемой корректности и стоимости блокировок.

## Prerequisites

Connections, SQL, WAL basics и consistency concepts.

## In scope

Transaction обеспечивает atomicity, consistency, isolation и durability. `COMMIT` делает изменения видимыми согласно выбранной модели, `ROLLBACK` отменяет незакоммиченные изменения. Autocommit превращает отдельный statement в transaction, если приложение не открыло явную.

Isolation определяет, какие конкурентные изменения видит transaction. Более строгая изоляция уменьшает anomaly, но может увеличить conflicts, retries и latency. Длинная transaction удерживает snapshot и мешает VACUUM.

## Диагностика

Проверяйте transaction age, open sessions, locks, wait events, commit/rollback rate и application retry. Нельзя оценивать transaction только по времени SQL statement.

## Типичные ошибки

- держать transaction во время сетевого вызова;
- забыть rollback после ошибки;
- использовать serializable без retry policy;
- считать commit мгновенным для replica;
- запускать migration в долгой transaction без оценки.

## Практика

Откройте две sessions, воспроизведите read/write concurrency на разных isolation levels и зафиксируйте видимость и ошибки serialization.

## Следующие темы

`04-MVCC-Locks-and-Deadlocks.md`, WAL и indexes.
