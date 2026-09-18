# Troubleshooting observability

## Цель

Отличать отказ системы от отказа telemetry и не принимать отсутствие сигнала за healthy состояние.

## Prerequisites

Prometheus, logs, traces, exporters, alerting и correlation.

## In scope

Проверяйте chain producer → agent/exporter → transport → storage → query → dashboard/alert. Для каждого звена нужны health metrics, queue/drop indicators и retention status.

Если metric отсутствует, возможны target down, scrape failure, relabel drop, query error, timestamp gap или cardinality overload. Если logs отсутствуют, проверяйте application output, collector, permissions, disk и delivery backpressure.

## Практика

Сломайте exporter, scrape network, remote write, log parser и alert route по отдельности. Для каждого подтвердите, что telemetry failure не скрывает user impact.

## Следующие темы

Reliability, distributed systems и Production incident investigation.
