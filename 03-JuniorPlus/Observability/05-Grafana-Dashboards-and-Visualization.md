# Grafana dashboards и визуализация

## Цель

Проектировать dashboard как инструмент принятия решения, а не как коллекцию случайных графиков.

## Prerequisites

Metrics/time series, PromQL, labels, SLI и incident investigation.

## In scope

Dashboard должен отвечать на operational questions: доступен ли сервис, каков user latency/error rate, где saturation и когда началась деградация. Панели группируют overview, traffic, errors, latency, resources и dependencies.

Каждый panel имеет units, time range, legends, thresholds и ссылку на следующий диагностический шаг. Один и тот же metric не следует показывать в разных несогласованных aggregations.

Dashboard не заменяет alert. Alert должен срабатывать по измеримому условию, а dashboard помогает понять scope и cause.

## Типичные ошибки

- десятки панелей без owner;
- отсутствие units/timezone;
- графики без labels или корреляции;
- alert threshold прямо в dashboard без SLO;
- dashboard показывает только host resources, но не user impact.

## Практика

Создайте dashboard для HTTP-сервиса: SLI overview, traffic, errors, latency, saturation и dependency panels. Проверьте его на искусственном latency spike.

## Следующие темы

`06-Alertmanager-Alerts-and-Routing.md`, logs и SLI investigation.
