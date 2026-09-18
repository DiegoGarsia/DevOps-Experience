# Observability: metrics, logs и alerts

## Три сигнала

- Metrics — числовые time series для aggregation, trends и alert thresholds;
- Logs — события с контекстом и причиной конкретного действия;
- Traces — путь одного request через components.

Observability отвечает, можно ли по внешнему поведению восстановить внутреннее состояние системы. Это не равно «установить dashboard».

## Prometheus model

```text
exporter/application → scrape target → Prometheus TSDB
                                      ├→ PromQL/Grafana
                                      └→ alert rule → Alertmanager → receiver
```

Labels определяют identity time series. High cardinality создаёт много series, расход RAM/disk и замедляет queries. Service discovery и scrape errors нужно отличать от отсутствия metric.

## Alerting

Alert rule определяет условие и `for`; Prometheus вычисляет expression; Alertmanager группирует, deduplicates, suppresses и routes notification. Alert должен быть actionable и связан с user impact.

## Logging

Structured logs с timestamp, level, service, request id и error cause легче искать. Loki/Promtail и ELK — разные реализации централизованного logging; выбор не меняет базовую модель ingestion, storage, query и retention.

## Диагностика No Data

```text
service discovery
↓
target UP/scrape error
↓
exporter endpoint
↓
metric name/labels
↓
PromQL/time range/retention
↓
Grafana datasource/panel
```

Blackbox probe проверяет внешний путь HTTP/TLS/DNS/TCP, exporter — внутреннее состояние сервиса. `probe_success=0` не заменяет latency/status breakdown.

## Вопросы

- **Почему logs недостаточно?** Logs подробны, но дороги для aggregation и не всегда есть при crash; metrics дают быстрый signal.
- **Что такое cardinality?** Число уникальных combinations labels для metric.
- **Почему alert fatigue опасна?** Noise снижает доверие и скрывает реальные incidents; нужны routing, grouping и actionable rules.

## Как объяснить за 30–60 секунд

«Observability связывает metrics, logs и traces с user impact. Prometheus scrape'ит targets и хранит series, Grafana визуализирует, Alertmanager маршрутизирует actionable alerts, а centralized logs дают detail. При No Data я иду от discovery и scrape к metric labels, query, datasource и retention».

[[03-JuniorPlus/Kubernetes/Networking]], [[04-Middle/Reliability/Availability-Backup-Recovery]], [[05-Production-Troubleshooting/Universal-Method]].

## Метрика и time series

Time series определяется именем metric и набором labels. `http_requests_total{service="api",status="500"}` и тот же metric с другим status — разные series. Высокая cardinality возникает, когда label содержит user id, request id или необязательный path. Это увеличивает RAM, disk и стоимость query.

Выбирай labels из ограниченного набора значений: service, method, status class. Request id ищи в logs/traces, а не в metric labels.

## Alert design

Alert должен отвечать:

- какой user impact возможен;
- сколько времени condition сохраняется;
- кто владелец;
- какие facts собрать;
- какой workaround безопасен;
- когда escalation.

`for` защищает от единичного шумового sample. Alertmanager grouping/deduplication предотвращают notification storm, но не должны скрывать разные root causes.

## Практическое задание

1. Создай exporter и scrape target.
2. Добавь metric с bounded labels и намеренно high-cardinality label.
3. Найди рост количества series и объясни его стоимость.
4. Создай alert с `for`, severity и runbook.
5. Сломай target discovery, exporter endpoint, query и Grafana datasource по отдельности.
6. Настрой log correlation через request id без помещения id в metric label.
