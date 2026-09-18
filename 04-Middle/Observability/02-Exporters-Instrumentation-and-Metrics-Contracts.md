# Exporters, instrumentation и metrics contracts

## Цель

Создавать telemetry contract, который остаётся стабильным при изменении приложения и не создаёт uncontrolled cardinality.

## Prerequisites

Time series, Prometheus scraping, labels, HTTP и application lifecycle.

## In scope

Exporter преобразует system/API data, instrumentation создаёт application signals. Contract фиксирует имя, type, units, labels, semantics, reset behavior и ownership.

Instrument endpoint должен быть дешёвым, защищённым и доступным в нужной network boundary. High-cardinality details уходят в logs/traces.

## Практика

Опишите metrics contract для HTTP service и exporter node resource; добавьте compatibility check и проверку cardinality.

## Следующие темы

`03-Log-Parsing-Labels-and-Retention.md`, Prometheus scale и SLO.
