# Prometheus scraping и exporters

## Цель

Понять pull-модель Prometheus, target discovery, scrape failure и роль exporter.

## Prerequisites

Metrics/time series, HTTP, labels, service discovery и network troubleshooting.

## In scope

Prometheus периодически scrapes HTTP metrics endpoint, добавляет target labels, хранит samples и предоставляет PromQL. Exporter преобразует чужой system/API format в Prometheus exposition format.

Scrape health — отдельный сигнал: target может отдавать metrics, но само приложение быть недоступным, и наоборот. Интервал, timeout, relabeling и target discovery должны соответствовать latency/cost.

## Диагностика

Проверяйте target state, last scrape, error, sample count, DNS/TLS, authentication и exporter process. Если metric отсутствует, отличайте target down, relabel drop, exporter error и неправильный query.

## Типичные ошибки

- scrape timeout больше interval;
- exporter не ограничивает cardinality;
- секрет в labels;
- считать target up application health;
- использовать одинаковый job/instance label для разных systems.

## Практика

Поднимите exporter, добавьте scrape target, намеренно сломайте DNS и TLS, восстановите target и подтвердите состояние через Prometheus UI/API.

## Следующие темы

`04-PromQL-and-Recording-Rules.md`, Grafana и Alertmanager.
