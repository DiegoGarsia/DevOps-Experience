# CI/CD troubleshooting guide

## Где остановился pipeline

```text
job created?
↓
Runner picked it?
↓
image/environment/variables?
↓
script/exit code?
↓
artifact/cache/needs?
↓
registry/deploy/verify?
```

Job не создан — `rules/source`; pending — Runner/tags/capacity; script failed — exact command and exit code; downstream failed — artifact, dotenv и `needs`.

## Build/registry

Сравни local и Runner environment, Dockerfile/context, architecture, network, credentials, CA, registry path и exact digest. Scan failure требует анализа package/CVE и rebuild, а не отключения gate.

## Deploy

`kubectl` acceptance не означает rollout success. Проверь image в Pod, ReplicaSet revision, events, readiness, Service endpoints, ingress/routing, smoke и metrics. Rollback возвращает known-good digest и проверяется тем же user path.

## Prevention

Build once, immutable tag/digest, protected variables, artifact contracts, serial production deployment, health gates, rollback rehearsal и pipeline loop protection.

[[05-Production-Troubleshooting/Universal-Method]], [[02-Junior/CI-CD/GitLab-Pipeline-Runner]], [[04-Middle/Architecture/Delivery-Architecture-and-Tradeoffs]].

## Сценарий: job не видит `NEXT_VERSION`

Проверь, была ли producer job создана, записала ли variable в dotenv report, объявлен ли artifact и есть ли `needs` с правильной dependency. Environment variable живёт внутри одного process/job, если её явно не передать artifact/report.

## Сценарий: pipeline зелёный, пользователи видят старую версию

Сравни commit, image tag/digest в Registry, Pod image IDs, ReplicaSet revision, Service endpoints, cache/proxy и rollout status. `kubectl apply` означает принятие object, а не то, что старый Pod исчез и traffic переключён.

## Prevention

Добавь CI lint, contract tests, immutable digest, artifact retention, deploy lock, smoke/metrics gate и rehearsal rollback. Логируй version/digest без secret.
