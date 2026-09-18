# Rebase, reset, restore и revert

## Цель

Различать операции над working tree, index и историей и выбирать безопасный способ исправления ошибки.

## Prerequisites

Branches, merge, remotes и index.

## In scope

`restore` меняет содержимое working tree или index относительно указанного источника. `reset` перемещает текущую branch и может менять index/working tree в зависимости от режима. `revert` создаёт новый commit, отменяющий эффект старого, поэтому подходит для уже опубликованной истории.

`rebase` переносит commits на новую базу, создавая новые object IDs. Он делает историю линейнее, но переписывает commits и опасен для shared branch.

```bash
git restore --staged file
git restore file
git revert <commit>
git rebase origin/main
```

Выбор:

- локальная незакоммиченная правка — `restore`;
- ошибочный локальный commit — осторожный `reset`;
- опубликованный ошибочный commit — `revert`;
- подготовка собственной feature branch — `rebase` при согласованной политике.

Перед переписывающими операциями создайте backup ref или убедитесь, что commit доступен через reflog.

## Типичные ошибки

- использовать `reset --hard` без backup;
- rebase shared branch;
- считать revert удалением commit;
- путать восстановление файла с отменой опубликованного изменения.

## Практика

Создайте ошибочный commit, исправьте его сначала через revert, затем в отдельной локальной ветке сравните reset и rebase. Найдите различия в history graph.

## Следующие темы

`07-Cherry-Pick-Reflog-and-Safe-Force-Push.md`.
