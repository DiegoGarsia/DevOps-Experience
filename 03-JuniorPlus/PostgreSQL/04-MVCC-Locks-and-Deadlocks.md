# MVCC, locks и deadlocks

## Цель

Понять, как PostgreSQL даёт concurrent reads через MVCC и почему блокировка или deadlock требуют анализа transaction order.

## Prerequisites

Transactions, isolation, processes и SQL updates.

## In scope

MVCC хранит версии строк и использует transaction IDs, чтобы session видела допустимый snapshot. UPDATE создаёт новую tuple version, а старая остаётся до тех пор, пока её можно видеть или удалить VACUUM.

Locks защищают row/table/schema resources. Deadlock возникает, когда transactions удерживают ресурсы в циклическом ожидании. PostgreSQL обнаруживает его и abort одну transaction; application должна уметь повторить безопасную операцию.

## Диагностика

Сопоставляйте `pg_stat_activity`, `pg_locks`, wait events, query text, transaction age и lock graph. Исправление обычно заключается в едином порядке доступа, коротких transactions и подходящем index, а не в бесконечном увеличении timeout.

## Типичные ошибки

- держать row lock во время внешнего вызова;
- обновлять таблицы в разном порядке;
- игнорировать idle in transaction;
- считать MVCC бесплатным для storage;
- убивать blocker без оценки rollback и application impact.

## Практика

В двух sessions создайте deadlock, зафиксируйте process/lock graph, затем измените порядок операций и подтвердите отсутствие цикла.

## Следующие темы

`05-WAL-Checkpoints-and-Buffers.md`, VACUUM и planner.
