# Cherry-pick, reflog и безопасный force push

## Цель

Научиться переносить отдельное изменение и восстанавливать потерянную локальную ссылку, не превращая переписывание истории в опасную привычку.

## Prerequisites

Refs, rebase, reset, revert и remote workflow.

## In scope

`cherry-pick` создаёт новый commit с изменениями выбранного commit на текущей базе. Это удобно для backport bugfix, но может перенести только часть контекста и создать duplicate changes.

Reflog локально записывает движения refs и `HEAD`. Он помогает найти commit после reset или удаления branch, пока запись не очищена garbage collection. Reflog обычно не является удалённым backup.

`--force-with-lease` разрешает переписать remote ref только если его известное состояние совпадает с ожиданием клиента. Это безопаснее `--force`, но всё равно требует review и коммуникации.

```bash
git cherry-pick <commit>
git reflog --date=local
git branch rescue <reflog-commit>
git push --force-with-lease origin feature/example
```

## Типичные ошибки

- cherry-pickять commit вместе с невыполненными prerequisites;
- считать reflog общим журналом сервера;
- применять `--force` к protected branch;
- не сообщать команде о переписывании истории;
- не проверять итоговый diff и тесты.

## Практика

Потеряйте локальный commit через reset, найдите его в reflog и создайте rescue branch. Затем перенесите отдельный fix через cherry-pick и сравните историю с merge.

## Следующие темы

CI/CD, immutable artifacts и практическая лаборатория Git recovery.
