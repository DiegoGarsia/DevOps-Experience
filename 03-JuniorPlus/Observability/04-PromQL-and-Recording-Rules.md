# PromQL и recording rules

## Цель

Научиться получать из raw series сигналы rate, error ratio, latency и saturation, пригодные для dashboard и alert.

## Prerequisites

Prometheus scraping, counters/gauges/histograms, labels и SLI concepts.

## In scope

PromQL выбирает series, фильтрует labels, агрегирует, вычисляет rate/increase, joins и quantiles для histogram. Query должна явно учитывать window, counter reset, missing data и grouping.

Recording rule заранее вычисляет дорогой или часто используемый выражение и сохраняет результат как новую series. Это уменьшает query latency, но добавляет evaluation cost и lifecycle для имени правила.

## Диагностика

Проверяйте raw samples, label match, range window, evaluation errors и freshness. Пустой результат может означать отсутствие series, неправильный matcher, scrape gap или неверный time range.

## Типичные ошибки

- alert на raw counter;
- `rate` на gauge;
- агрегация без сохранения нужного label;
- quantile без понимания histogram buckets;
- recording rule скрывает изменение исходной семантики.

## Практика

Постройте PromQL для request rate, error ratio и p95 latency, затем вынесите часто используемый результат в recording rule и сравните стоимость query.

## Следующие темы

`05-Grafana-Dashboards-and-Visualization.md`, Alertmanager и SLI.
