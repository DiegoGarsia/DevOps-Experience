# Ingress, Gateway и traffic entry

## Цель

Понимать путь внешнего HTTP/TLS-трафика до Service и управлять routing, certificates и failure behavior.

## Prerequisites

HTTP/TLS, reverse proxy, Services, EndpointSlice, DNS и load balancing.

## In scope

Ingress resource описывает host/path routing, но controller реализует dataplane. Gateway API отделяет listener, route и backend policy и лучше выражает ownership. TLS termination, SNI, redirects, timeouts, body limits и client IP должны быть частью design.

## Диагностика

Сопоставляйте DNS address, external load balancer, controller logs, listener/certificate, route match, Service endpoints и backend response. Разделяйте default backend, 404 route mismatch, 502 upstream и TLS failure.

## Типичные ошибки

- ресурс Ingress есть, controller отсутствует;
- сертификат относится к другому SNI;
- path rewrite ломает API;
- внешний health check не совпадает с readiness;
- public endpoint ведёт в internal Service без policy.

## Практика

Настройте два host/path route с TLS, добавьте backend failure и проверьте 404/502/certificate scenarios по логам и metrics.

## Следующие темы

`06-CNI-and-Pod-Networking.md`, NetworkPolicy и production proxy.
