# Dashboard design и operational views

## Цель

Строить views для разных решений: operator, incident commander, service owner и capacity planner.

## Prerequisites

Metrics/logs/traces correlation, SLI, PromQL и alerting.

## In scope

Overview показывает user impact, drill-down — service/dependency, resource view — saturation, release view — change correlation, capacity view — trend/forecast. Каждая панель имеет units, owner, query и interpretation.

Dashboard должен оставаться читаемым при outage, показывать missing data и ссылаться на runbook.

## Практика

Сделайте четыре operational views для одного сервиса и проведите incident simulation: участник должен найти scope и следующий тест без подсказки.

## Следующие темы

`08-Observability-Troubleshooting.md`, SLO и Production investigation.
