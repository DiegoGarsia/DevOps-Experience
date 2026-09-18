# Streaming replication и replication lag

## Цель

Понять, как WAL передаётся standby и как lag влияет на read scaling, failover и RPO.

## Prerequisites

WAL, transactions, backup, PostgreSQL processes и TCP.

## In scope

Primary генерирует WAL, standby получает и replay-ит его. Replication может быть asynchronous или synchronous; commit semantics и availability отличаются. Lag бывает transport, write или replay lag.

Диагностика сравнивает LSN primary/standby, replay timestamp, WAL retention, network throughput и receiver/replay process.

## Типичные ошибки

- читать со stale replica без SLA;
- считать connected standby актуальным;
- удалять WAL до достижения standby;
- synchronous replication без capacity/latency plan;
- promotion standby с большим неизвестным lag.

## Практика

Настройте streaming replica, создайте write load, задержите replay, измерьте lag и определите safe/read-only behavior.

## Следующие темы

`02-HA-Failover-and-Patroni-Concepts.md`, PITR и troubleshooting.
