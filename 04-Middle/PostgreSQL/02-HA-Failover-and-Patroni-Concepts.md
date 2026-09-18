# HA/failover и концепции Patroni

## Цель

Понять, что HA — это выбор нового leader плюс fencing, routing, data safety и client reconnection.

## Prerequisites

Streaming replication, quorum, distributed systems, DNS/load balancing и backup.

## In scope

Failover должен исключить split-brain: старый primary нельзя позволить писать после promotion нового. DCS/consensus хранит leader lock, Patroni управляет PostgreSQL lifecycle и проверяет replication state, но не заменяет backup или корректную application retry policy.

## Диагностика

Проверяйте leader identity, DCS quorum, timeline, replication lag, fencing, client endpoint и connection pool.

## Типичные ошибки

- promotion без fencing;
- DNS/Pooler продолжает вести к старому primary;
- failover при неизвестном lag;
- synchronous policy не соответствует RPO;
- HA тестируется только выключением процесса.

## Практика

Проведите controlled switchover и failure failover, зафиксируйте timeline, client reconnect, lag и recovery old primary.

## Следующие темы

`03-Connection-Pooling-and-Session-Management.md`, PITR и DR.
