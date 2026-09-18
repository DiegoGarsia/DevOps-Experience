# Лаборатория 04: observability и incident

## Цель

Научиться переходить от user symptom к root cause через metrics, logs и traces/контекст.

## Шаги

1. Добавь request rate, error rate, latency и saturation metrics.
2. Настрой structured logs с request id.
3. Настрой alert на user impact, а не только на process existence.
4. Создай dashboard с переходом от service к Pod и log query.
5. Сымитируй slow dependency, disk pressure, registry outage или failed rollout.
6. Проведи incident по [[05-Production-Troubleshooting/Incident-Response]].
7. После mitigation выполни postmortem и добавь prevention action.

## Критерий

Для каждого alert должны быть: scope, severity, expected response, runbook, suppression/grouping и проверка, что notification действительно дошло.

[[03-JuniorPlus/Observability/Metrics-Logs-Alerts]], [[04-Middle/Reliability/Availability-Backup-Recovery]], [[05-Production-Troubleshooting/Universal-Method]].

## Ожидаемые результаты

- alert содержит symptom, severity, owner и runbook;
- metric связывается с service/Pod, log — с request id;
- No Data различается по discovery, scrape, query, datasource и retention;
- incident timeline содержит факты, изменения и mitigation;
- postmortem содержит root cause и prevention action с owner/deadline.

## Намеренно сломанные сценарии

1. Останови exporter → target down, но application может быть healthy.
2. Измени label → query становится пустым.
3. Добавь high-cardinality label → Prometheus resource pressure.
4. Сломай Loki/Promtail delivery → metrics остаются, logs исчезают.
5. Создай false alert без `for` → notification storm.
6. Сымитируй slow dependency → latency/error alert и incident response.

## Definition of Done

Ты можешь ответить: какой user impact, где evidence, почему alert сработал, какой workaround безопасен, как проверить восстановление и что предотвращает повтор.
