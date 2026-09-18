# Connection pooling и session management

## Цель

Управлять количеством database connections и не переносить session state между клиентами.

## Prerequisites

PostgreSQL connections, transactions, locks, HA и application timeouts.

## In scope

Pooler переиспользует backend connections и ограничивает concurrency. Session pooling сохраняет session state дольше, transaction pooling требует корректного использования prepared statements, temporary tables, advisory locks и session settings.

Total pool capacity должна учитывать database max_connections, replicas, migrations, admin access и failover surge. Pool не должен превращать slow database в бесконечную очередь.

## Практика

Сравните direct connections и pooler под нагрузкой, задайте timeout/queue limit и смоделируйте отказ primary.

## Следующие темы

`04-Query-Performance-and-Index-Design.md`, retries и capacity.
