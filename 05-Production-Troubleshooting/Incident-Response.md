# Incident response и postmortem

## Во время incident

1. Назначь incident lead и канал коммуникации.
2. Зафиксируй impact, scope, timestamp и severity.
3. Заморозь опасные changes.
4. Выбери безопасный mitigation: rollback, failover, rate limit, traffic reduction.
5. Проверяй user path и обновляй timeline.
6. Escalate владельцу application/data/security по сигналам, а не по догадкам.

## Workaround и fix

Workaround возвращает service, но может оставить defect. Permanent fix устраняет механизм. Prevention закрывает detection/design/process gap: test, alert, guardrail, capacity, rollback drill или documentation.

## Postmortem

Без обвинений зафиксируй:

- impact и duration;
- timeline изменений и симптомов;
- root cause и contributing factors;
- почему monitoring/rollback не сработали;
- corrective/preventive actions с owner и сроком;
- evidence, который подтвердил вывод.

Не называй причиной «человек ошибся»: ищи отсутствие validation, unsafe default, недостаток permissions, отсутствующий test или слабую observability.

## Вопросы

- **Когда rollback?** Когда known-good state безопаснее дальнейшей диагностики и rollback сам проверен.
- **Почему не менять всё сразу?** Потеряешь causal evidence и увеличишь scope.
- **Что делает postmortem полезным?** Проверяемые actions, а не только описание происшествия.

[[05-Production-Troubleshooting/Universal-Method]], [[04-Middle/Reliability/Availability-Backup-Recovery]].

## Минимальный incident template

```text
Incident ID:
Начало и обнаружение:
Affected users/services:
Severity:
Incident lead:
Known changes:
Current symptom:
Evidence:
Hypotheses:
Mitigation:
Verification:
Root cause:
Prevention actions/owner/deadline:
```

Плохой postmortem говорит «перезапустили и заработало». Хороший связывает timeline, evidence, mechanism и prevention. Severity определяется impact/urgency, а не тем, какой компонент кажется важнее.
