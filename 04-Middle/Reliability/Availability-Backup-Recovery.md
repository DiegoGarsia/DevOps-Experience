# Reliability: availability, backup и recovery

## Термины

- availability — доля времени/requests с допустимым результатом;
- reliability — вероятность выполнять функцию без отказа за период;
- redundancy — несколько компонентов вместо одного;
- SPOF — компонент, отказ которого ломает service;
- RPO — сколько данных допустимо потерять;
- RTO — сколько времени допустимо восстанавливаться;
- graceful degradation — полезный ограниченный режим вместо полного outage.

## Recovery model

```text
failure → detection → containment → failover/restore
→ verification → traffic return → root cause → prevention
```

Backup без проверенного restore не является доказательством восстановления: может быть повреждён формат, отсутствовать key/secret, потеряться attachments или неверно выбран endpoint.

## Backup design

Определи scope, consistency point, retention, off-host copy, encryption, access, checksum, restore test и deletion protection. Snapshot полезен для rollback, replication — для availability, backup — для recovery от logical error и disaster.

## Health checks

Liveness отвечает «нужно ли перезапустить», readiness — «можно ли дать traffic», synthetic/blackbox — «видит ли client рабочий путь». Нельзя использовать liveness как dependency probe без защиты от restart storm.

## Middle-вопросы

- **Почему replica не backup?** Ошибка или удаление может синхронно попасть на replica.
- **Почему redundancy не гарантирует availability?** Common network, power, credentials, DNS или operator остаются SPOF.
- **Как измерить готовность?** Restore game: время, результат, data correctness и user-path test.

## 30–60 секунд

«Reliability — это не отсутствие отказов, а контролируемое поведение при них. Я определяю RPO/RTO, SPOF, detection и recovery path. Replication и snapshot помогают availability/rollback, но только независимый и restore-tested backup доказывает recoverability».

[[03-JuniorPlus/Kubernetes/Storage-Config-Secrets-RBAC]], [[03-JuniorPlus/Databases/PostgreSQL-Internals]], [[05-Production-Troubleshooting/Incident-Response]].

## Availability как модель

Availability можно оценивать как долю времени или долю успешных requests. Один single-node компонент является SPOF, даже если приложение имеет несколько replicas. Redundancy полезна только если failure domains независимы: разные nodes, storage, network path, credentials и operator assumptions.

## RPO/RTO на примере

Если RPO равен 15 минутам, backup/replication должны гарантировать потерю не более 15 минут при выбранном failure. Если RTO равен 30 минутам, restore path, DNS, secrets, capacity и validation должны укладываться в 30 минут. Без restore drill это только обещание.

## Практическое задание

1. Опиши RPO/RTO для PostgreSQL и attachments отдельно.
2. Удали тестовую logical record и восстанови её из backup.
3. Сымитируй отказ node, registry и DNS по очереди.
4. Измерь detection, mitigation и verification time.
5. Найди common dependency, которая остаётся SPOF несмотря на две replicas.
