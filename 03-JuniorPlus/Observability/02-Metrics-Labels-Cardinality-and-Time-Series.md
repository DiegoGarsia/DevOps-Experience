# Метрики, labels, cardinality и time series

## Цель

Понимать модель временного ряда и проектировать labels так, чтобы метрики оставались полезными и доступными по стоимости.

## Prerequisites

Metrics/logs/traces model, counters/gauges и HTTP service behavior.

## In scope

Time series определяется metric name и набором label values. Counter растёт, gauge может увеличиваться и уменьшаться, histogram описывает распределение через buckets/count/sum. Rate вычисляется по изменению counter с учётом reset.

Cardinality — число уникальных label combinations. User ID, request ID, URL с переменными параметрами и error message могут взорвать series count. Labels должны описывать ограниченный набор категорий; высокую детализацию оставляйте logs/traces.

## Диагностика

Следите за series count, scrape size, query latency, missing samples и label churn. Проверяйте единицы измерения и reset counters после restart.

## Типичные ошибки

- label с неограниченными значениями;
- histogram buckets без latency SLO;
- использовать gauge для cumulative event;
- сравнивать raw counter вместо rate;
- игнорировать scrape failures.

## Практика

Добавьте метрики requests, errors и latency, затем введите label с request path и сравните cardinality для нормализованного и сырого URL.

## Следующие темы

`03-Prometheus-Scraping-and-Exporters.md`, PromQL и alert design.
