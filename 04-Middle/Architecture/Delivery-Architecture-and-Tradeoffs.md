# Delivery architecture и trade-offs

## Базовый поток

```text
Git change
↓
validation/test/security
↓
immutable image
↓
Registry
↓
deploy
↓
health/smoke/metrics
↓
promotion или rollback
```

Каждый переход должен иметь contract: input, output, identity, permissions, timeout и failure behavior. «Job завершился успешно» не означает, что пользователи получили новую рабочую версию.

## Выбор подхода

- Rolling экономит ресурсы, но смешивает версии и требует backward compatibility.
- Blue-green проще переключить и откатить, но временно требует двойной capacity.
- Canary уменьшает blast radius, но требует routing, observability и rollback automation.
- Push CD быстрее внедрить, GitOps даёт audit/reconciliation, но добавляет controller и задержку convergence.
- mutable tag удобен, immutable digest даёт воспроизводимость.

## Безопасный promotion

```text
build once
→ sign/scan
→ deploy exact digest
→ readiness
→ smoke
→ metrics/error budget
→ promote
→ retain rollback artifact
```

Production deploy должен быть serialised, observable и reversible. Rollback должен возвращать known-good version, а не пересобирать её в incident.

## Security boundaries

Разделяй developer, Runner, Registry, Kubernetes API и workload identities. Runner не должен иметь cluster-admin. Secrets передаются через protected mechanism, не через logs или image layers.

## Middle-вопросы

- **Почему healthcheck недостаточен?** Он может проверять процесс, но не user path, dependency, latency и data correctness.
- **Что важнее: скорость deploy или rollback?** Нужен измеримый risk budget; быстрый deploy без доказуемого rollback увеличивает outage.
- **Где находится source of truth?** Явно выбрать Git, registry digest, cluster state или database, иначе reconciliation конфликтует.

## 30–60 секунд

«Я проектирую delivery как цепочку проверяемых contracts: commit превращается в immutable artifact, artifact разворачивается, результат проверяется по слоям, и только затем выполняется promotion. Trade-off между rolling, blue-green, canary и GitOps определяется blast radius, capacity, compatibility и сложностью rollback».

[[02-Junior/CI-CD/GitLab-Pipeline-Runner]], [[04-Middle/Reliability/Availability-Backup-Recovery]], [[05-Production-Troubleshooting/CI-CD]].

## Термины delivery

Immutable artifact — результат, который нельзя незаметно изменить после build. Promotion — перевод уже проверенного artifact в следующее окружение. Health gate — условие, которое должно пройти до следующего шага. Reconciliation — приведение runtime к декларативному desired state.

## Архитектурный пример

```text
commit
→ tests/security
→ image digest
→ Registry
→ deploy digest
→ readiness
→ smoke
→ error rate/latency
→ promotion
```

Если build повторяется на этапе rollback, результат может отличаться из-за base image, dependency или времени. Поэтому rollback возвращает сохранённый digest.

## Failure scenario

Новая версия Ready, но error rate вырос. Workaround — остановить promotion и вернуть stable. Root cause может быть schema incompatibility, feature flag, downstream timeout или traffic skew. Permanent fix требует evidence из logs/metrics/traces, а prevention — compatibility test, canary gate или migration design.

## Практическое задание

1. Спроектируй pipeline с immutable digest.
2. Добавь ручной approval между canary и promotion.
3. Сымитируй health pass при HTTP 200, но error rate 5%.
4. Напиши rollback, который возвращает прежний digest.
5. Объясни, какие credentials нужны каждому этапу и почему Runner не должен иметь cluster-admin.
