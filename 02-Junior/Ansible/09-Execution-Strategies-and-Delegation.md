# Execution strategies и delegation

## Цель

Понимать, где и в каком порядке выполняется task при нескольких hosts, batches и delegated operations.

## Prerequisites

Inventory groups, variables, roles, idempotency и SSH.

## In scope

Ansible может выполнять task linear по batch, free независимо, serial ограничивать batch и throttle ограничивать concurrency конкретной task. `run_once` выполняет task один раз, но нужно понимать, на каком host будут доступны variables/facts.

`delegate_to` запускает task на другом host, например registration у load balancer или создание записи во внешней системе. Data и ownership должны быть явными: действие относится к target host или delegate host?

При rolling change сначала подготовьте batch, проверьте health, затем переходите к следующему. Ошибка в serial deployment должна остановить дальнейшее распространение.

## Типичные ошибки

- delegate task меняет не тот environment;
- `run_once` скрывает отсутствие данных на host;
- free strategy нарушает предположение о порядке;
- serial без health gate;
- concurrency перегружает API.

## Практика

Выполните rolling configuration на группе web с `serial`, добавьте delegate health check и остановите rollout после failure первого batch.

## Следующие темы

`10-Ansible-Testing-and-Linting.md`, dynamic inventory и large-scale execution.
