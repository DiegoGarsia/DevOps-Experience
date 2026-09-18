# Probes и application health

## Цель

Различать startup, readiness и liveness и не превращать health checks в источник cascading failure.

## Prerequisites

Pods, Services, HTTP/API, lifecycle и graceful shutdown.

## In scope

Startup probe даёт приложению время и откладывает остальные проверки. Readiness решает, направлять ли новый traffic. Liveness решает, нужен ли restart. Probe может быть HTTP, TCP или exec и имеет timeout, period, threshold и initial delay.

Health endpoint должен быть дешёвым, детерминированным и соответствовать цели проверки. Readiness может учитывать критическую dependency, liveness обычно не должен перезапускать приложение из-за временной внешней ошибки.

## Диагностика

Проверяйте probe event, response/status, latency, endpoint path, container port, NetworkPolicy и application logs. Сопоставляйте probe failure с user traffic и restart count.

## Типичные ошибки

- liveness проверяет внешнюю базу;
- слишком короткий timeout;
- probe создаёт тяжёлый запрос;
- readiness не снимает Pod при graceful shutdown;
- одинаковые probe для startup и liveness.

## Практика

Добавьте три probe к приложению с медленным стартом, dependency failure и controlled shutdown, затем измерьте влияние на Service endpoints.

## Следующие темы

`20-Requests-Limits-and-QoS.md`, Deployment rollout и HPA.
