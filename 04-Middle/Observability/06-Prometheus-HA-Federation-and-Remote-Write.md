# Prometheus HA, federation и remote write

## Цель

Понимать trade-offs отказоустойчивости, долгого хранения и стоимости metrics platform.

## Prerequisites

Scraping, PromQL, labels, retention, remote storage и HA concepts.

## In scope

Два Prometheus экземпляра повышают availability, но создают duplicate series и требуют alert deduplication. Federation выбирает часть агрегированных metrics, remote write отправляет samples во внешнее хранилище и добавляет backpressure/queue failure modes.

Нужно определить source of truth, query path при outage, retention tiers, cardinality budget и recovery procedure.

## Практика

Настройте два collectors, remote write outage и queue saturation, измерьте потерю samples и восстановление без overload.

## Следующие темы

`07-Dashboard-Design-and-Operational-Views.md`, cardinality и SLO.
