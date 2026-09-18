# Rules, needs и parallelism

## Цель

Научиться запускать только нужные jobs и строить DAG pipeline без скрытых гонок и пропущенных проверок.

## Prerequisites

Jobs, stages, variables, artifacts и trust boundaries.

## In scope

Rules используют branch, tag, изменённые paths, source pipeline и variables. `needs` выражает прямую зависимость и позволяет job стартовать после конкретных upstream jobs, не дожидаясь всего stage.

Parallel matrix ускоряет одинаковые проверки, но увеличивает стоимость runner и нагрузку на внешние системы. Артефакты и cache должны иметь уникальные keys, иначе jobs могут перезаписать результат друг друга.

## Диагностика

Проверяйте, почему job не создан, skipped или pending: rule evaluation, dependency graph, runner capacity, artifact availability и protected context.

## Типичные ошибки

- rule для merge request случайно допускает production deploy;
- `needs` ссылается на job, который может быть skipped;
- parallel jobs используют один mutable workspace;
- cache key не включает dependency lock;
- ускорение pipeline скрывает обязательную проверку.

## Практика

Создайте pipeline для branches, tags и merge requests с разными rules, добавьте matrix test и намеренно сделайте upstream job skipped.

## Следующие темы

`07-Environments-Deployments-and-Approvals.md`, promotion и rollback.
