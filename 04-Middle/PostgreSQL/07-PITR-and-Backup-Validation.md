# PITR и validation backup

## Цель

Восстановить PostgreSQL к выбранному моменту и доказать, что backup chain соответствует RPO/RTO.

## Prerequisites

WAL/archive, base backup, restore, time synchronization и recovery procedure.

## In scope

PITR требует valid base backup, непрерывный WAL archive до target time, recovery configuration и отдельное место восстановления. Target time должен быть подтверждён application timeline и timezone.

Validation включает restore, consistency checks, roles/extensions, application smoke test и измерение времени. Backup success без restore evidence недостаточен.

## Практика

Создайте test backup/WAL archive, внесите несколько изменений, восстановите database до времени перед последней операцией и подтвердите данные и RPO.

## Следующие темы

`08-PostgreSQL-Troubleshooting.md`, HA and disaster recovery.
