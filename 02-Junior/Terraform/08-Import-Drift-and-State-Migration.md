# Import, drift и state migration

## Цель

Научиться принимать существующую инфраструктуру под управление без пересоздания и безопасно менять адресацию state.

## Prerequisites

State, resources, plan, remote locking и module contracts.

## In scope

Import связывает уже существующий remote object с resource address в state. После import configuration должна описывать объект так, чтобы plan не предлагал неожиданный replacement.

Drift — расхождение между state/configuration и реальной инфраструктурой. Оно возникает из-за ручного изменения, внешнего controller, defaults provider или неполного refresh.

State migration меняет адрес resource без удаления объекта. Используйте документированные операции state move/import, backup state и plan review. Любая migration должна иметь rollback strategy.

## Диагностика

Сначала сохраните backup state, выполните refresh/plan, классифицируйте diff: legitimate change, drift, provider upgrade или неправильная configuration.

## Типичные ошибки

- import без последующего plan;
- исправление drift через destroy;
- ручное редактирование JSON state;
- migration без lock и backup;
- скрытие drift через `ignore_changes`.

## Практика

Создайте ресурс вручную, импортируйте его, напишите matching configuration, внесите внешний drift и выполните безопасную migration address.

## Следующие темы

`09-Workspaces-Testing-and-Troubleshooting.md` и JuniorPlus state operations.
