# RPO, RTO и recovery strategies

## Цель

Связать бизнес-допуск потери данных/простоя с технической backup, replication и failover strategy.

## Prerequisites

Backup/restore, replication, availability, capacity и application ownership.

## In scope

RPO — допустимая потеря данных во времени, RTO — допустимое время восстановления. Near-zero RPO может требовать synchronous replication и снижать availability; короткий RTO требует automation, warm capacity и tested runbook.

## Практика

Для stateless web и PostgreSQL выберите RPO/RTO, design backup/replication/failover и проведите tabletop recovery.

## Следующие темы

`05-Backup-Restore-and-Recovery-Testing.md`, DR architecture и Production game day.
