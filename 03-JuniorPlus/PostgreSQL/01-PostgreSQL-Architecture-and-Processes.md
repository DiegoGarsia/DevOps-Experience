# Архитектура PostgreSQL и процессы

## Цель

Понять, как PostgreSQL принимает connection, выполняет запрос и сохраняет данные через server processes, shared memory и data directory.

## Prerequisites

Linux processes, filesystems, storage, TCP и базовый SQL.

## In scope

Клиент подключается к postmaster, который создаёт backend process для session. Shared buffers, WAL buffers, background writer, checkpointer, WAL writer и autovacuum workers обслуживают разные части lifecycle данных.

Data directory содержит catalog, relation files, WAL и configuration. Backend process выполняет parse, plan, lock и execution, а storage durability зависит от WAL, fsync и checkpoint behavior.

## Диагностика

Сопоставляйте process list, active sessions, data directory, logs, connections и disk I/O. PostgreSQL process name сам по себе не показывает, какой query вызвал нагрузку.

## Типичные ошибки

- запускать несколько cluster на одном data directory;
- копировать data directory без consistency;
- считать backend process отдельной базой;
- игнорировать connection limit и memory settings;
- менять configuration без reload/restart check.

## Практика

Установите PostgreSQL, найдите server processes, создайте session, сопоставьте PID с backend и измените параметр, требующий reload.

## Следующие темы

`02-Databases-Schemas-Roles-and-Connections.md`, transactions и WAL.
