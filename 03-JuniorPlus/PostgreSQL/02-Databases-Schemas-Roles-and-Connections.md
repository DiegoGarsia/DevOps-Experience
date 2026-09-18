# Databases, schemas, roles и connections

## Цель

Различать database, schema, object ownership, role и client connection при проектировании доступа и capacity.

## Prerequisites

PostgreSQL processes, Linux permissions, TCP и SQL basics.

## In scope

Database — логическое пространство с catalog и sessions. Schema группирует objects внутри database. Role может быть login role или group role и получает privileges через ownership/direct grants/membership.

Connection создаёт backend process, поэтому max connections имеет стоимость памяти. Pooling уменьшает количество database sessions, но требует правильной transaction/session state handling.

Privileges должны выдаваться на schema/table/function по принципу least privilege. `public` defaults необходимо проверить, особенно для service role.

## Диагностика

Сопоставляйте `pg_stat_activity`, `pg_roles`, `information_schema`, grants и application connection string. Отличайте authentication, authorization, database not found и exhausted connection slots.

## Типичные ошибки

- приложение подключается superuser;
- grant на schema не даёт table privilege;
- забыть default privileges для будущих objects;
- создать отдельную database вместо schema без причины;
- connection pool больше database capacity.

## Практика

Создайте application role с ограниченными правами, отдельную migration role, database/schema и проверьте allowed/denied operations.

## Следующие темы

`03-Transactions-ACID-and-Isolation.md`, connections и pooling.
