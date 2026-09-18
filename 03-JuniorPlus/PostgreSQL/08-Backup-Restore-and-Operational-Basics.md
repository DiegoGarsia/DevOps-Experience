# Backup, restore и operational basics PostgreSQL

## Цель

Научиться выполнять логический и физический backup, проверять restore и связывать процедуру с RPO/RTO.

## Prerequisites

PostgreSQL architecture, WAL, storage, roles, permissions и recovery model.

## In scope

Logical backup сохраняет SQL representation объектов и данных; physical/base backup копирует cluster files с учётом WAL. `pg_dump` удобен для database/object migration, но не заменяет physical recovery крупного кластера.

Backup lifecycle включает encryption, retention, offsite copy, catalog/credential preservation, integrity check и test restore. Снимок успешного backup без восстановления не доказывает recoverability.

При restore нужно учитывать version compatibility, extensions, roles, ownership, network identity и application quiescence.

## Диагностика

Проверяйте exit code, backup size, checksum, WAL availability, restore logs и application query. Измеряйте фактическое RPO/RTO, а не только ожидаемое.

## Типичные ошибки

- backup только data без roles/config;
- хранить копию на том же failure domain;
- не включить требуемый WAL;
- restore поверх production без isolation;
- никогда не проверять последний backup.

## Практика

Создайте logical backup test database, восстановите в отдельную database, сравните schema/data и оформите процедуру и recovery evidence.

## Следующие темы

PostgreSQL Middle replication, PITR, pooling и troubleshooting.
