# Distributed tracing и OpenTelemetry concepts

## Цель

Использовать trace/span для поиска задержки и ошибки в распределённом request path.

## Prerequisites

HTTP connection lifecycle, correlation ID, logs, metrics и service boundaries.

## In scope

Trace состоит из spans, каждый описывает operation, duration, attributes, events и status. Context propagation связывает spans между services. Sampling уменьшает cost, но влияет на полноту evidence.

OpenTelemetry разделяет instrumentation, SDK, collector и backend. Collector может batch/filter/enrich/retry, но не должен скрывать overload и потерю telemetry.

## Практика

Инструментируйте два сервиса и database call, передайте trace context, создайте slow dependency и найдите span с максимальной latency.

## Следующие темы

`05-Metrics-Logs-and-Traces-Correlation.md`, Prometheus scale и incident investigation.
