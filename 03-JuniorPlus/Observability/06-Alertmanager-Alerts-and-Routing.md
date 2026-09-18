# Alertmanager, alerts и routing

## Цель

Научиться превращать metric condition в actionable notification без alert storm и потери критических сигналов.

## Prerequisites

PromQL, recording rules, SLI/SLO и incident response basics.

## In scope

Alert rule определяет condition, duration, labels и annotations. Alertmanager группирует, deduplicates, silences и маршрутизирует alerts по service, severity, team и environment. Notification должен содержать symptom, scope, dashboard/runbook и owner.

Alert должен иметь signal-to-noise rationale, inhibition policy и тест routing. Paging подходит для action now, ticket — для planned work, dashboard — для investigation.

## Диагностика

Проверяйте rule evaluation, pending/firing state, label match, Alertmanager route, silence, inhibition и delivery channel. Отсутствие notification не всегда означает отсутствие firing alert.

## Типичные ошибки

- alert на каждый instance без grouping;
- отсутствие `for` для шумного сигнала;
- page без runbook/owner;
- silence без expiration;
- duplicate notifications при HA.

## Практика

Создайте warning и critical alert для test service, настройте grouping/routing, добавьте silence и проверьте, что критический alert не подавляется ошибочно.

## Следующие темы

`07-Logs-Loki-and-Blackbox-Monitoring.md`, SLI и incident investigation.
