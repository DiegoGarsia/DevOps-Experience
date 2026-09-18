# Recording rules и design alerts

## Цель

Проектировать производительные query и alerts, которые отражают user impact и приводят к действию.

## Prerequisites

PromQL, cardinality, SLI, dashboards и incident response.

## In scope

Recording rule предварительно вычисляет дорогой выражение; alert rule определяет condition, duration, labels, annotations и severity. Alert должен иметь owner, runbook, threshold rationale и тест notification path.

Design проверяет missing data, scrape outage, aggregation labels, reset counters и multi-instance behavior. Alerting на symptom предпочтительнее alerting на внутренний resource без user impact.

## Практика

Создайте SLI-based availability/latency alerts с warning/critical, group/inhibit policy и тестом delayed/missing metric.

## Следующие темы

`02-Exporters-Instrumentation-and-Metrics-Contracts.md`, log/tracing correlation.
