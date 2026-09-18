# Универсальный метод расследования

## Алгоритм

```text
Symptom
↓
Scope
↓
Layer
↓
State
↓
Facts
↓
Hypothesis
↓
Test
↓
Fix/workaround
↓
Verify
↓
Root cause
↓
Prevention
```

## Термины

- Symptom — наблюдаемый эффект: 502, latency, restart;
- Cause — непосредственный механизм, например missing route;
- Root cause — почему система пришла в это состояние, например изменение без validation;
- Workaround — временно возвращает service;
- Permanent fix — устраняет механизм отказа;
- Prevention — тест, alert, policy или design change, предотвращающий повтор.

Не перепрыгивай от symptom к restart. Сначала зафиксируй время, scope, affected users, recent changes, known-good comparison и evidence.

## Границы слоёв

```text
client → DNS → network → TLS → proxy → platform → workload → dependency → data
```

Одна команда должна отвечать на один вопрос. Если hypothesis не подтверждена, не превращай workaround в root cause.

## Закрытие инцидента

Проверь user path, error rate, latency, saturation, logs и rollout state. Затем зафиксируй impact, timeline, root cause, detection gap, corrective actions, owner и срок.

## Вопросы

- **Когда останавливать диагностику?** Когда user impact стабилен, known-good state восстановлен и есть evidence, а не только исчезший symptom.
- **Почему restart не root cause?** Он меняет state и может скрыть defect, но не объясняет возникновение.

[[05-Production-Troubleshooting/Incident-Response]], [[04-Middle/Reliability/Availability-Backup-Recovery]].

## Полный шаблон расследования

### Симптом
Запиши точный status, latency/error rate, первый timestamp и affected user path.

### Scope
Определи, затронут один client, один Pod, один node, один region, один endpoint или вся система.

### Layer
Раздели DNS, network, TLS, proxy, platform, workload, dependency и data.

### Facts
Собери commands, metrics, logs, events, configuration и recent changes. Не меняй систему до сохранения ключевого evidence, если это безопасно.

### Hypotheses
Сформулируй 2–3 проверяемых объяснения. Для каждой укажи наблюдение, которое подтвердит или опровергнет её.

### Test и interpretation
Одна проверка должна менять вероятность одной hypothesis. Если результат не различает гипотезы, следующая проверка выбрана плохо.

### Workaround и permanent fix
Workaround снижает impact; permanent fix исправляет механизм; prevention закрывает detection/design/process gap.

### Verification
Проверь user path, error rate, latency, saturation, logs, replicas, data correctness и отсутствие secondary failure.

## Практическое задание

Выбери любой сервис и составь incident card с symptom, scope, layer, facts, hypotheses, test, workaround, verification, root cause и prevention. Затем намеренно сломай только один слой и повтори расследование без подсматривания в решение.
