# Эксплуатация load balancer и health checks

## Цель

Научиться выводить backend из rotation, распределять трафик и проверять readiness без ложных отказов.

## Prerequisites

Reverse proxy, TCP, HTTP, health-check model и basic application behavior.

## In scope

Load balancer поддерживает pool upstream и применяет алгоритм выбора: round-robin, weighted, least connections или hash. Для long-lived connections распределение может быть неравномерным, а session affinity усложняет failover.

Health check бывает TCP, HTTP или application-level. Readiness должна отвечать на вопрос «можно ли принимать новый трафик», а liveness — «нужно ли перезапустить процесс». Проверка должна иметь timeout, interval, failure threshold и recovery behavior.

Drain не принимает новые соединения, но даёт активным запросам завершиться. Без drain rolling deployment может оборвать пользовательские операции.

## Диагностика

Проверяйте число healthy/unhealthy endpoints, transitions, распределение requests, retries, active connections и latency по backend. Сравнивайте health endpoint с реальной пользовательской операцией.

## Типичные ошибки

- health check проверяет только TCP port;
- readiness зависит от недоступной внешней системы без timeout;
- backend удаляется при кратком сетевом spike;
- нет connection draining;
- несколько уровней retry создают retry storm.

## Практика

Настройте pool из трёх backend, остановите один, затем добавьте медленный backend и сравните поведение round-robin и least connections.

## Следующие темы

`05-Host-Firewall-and-NAT-Operations.md`, Kubernetes Services и Ingress.
