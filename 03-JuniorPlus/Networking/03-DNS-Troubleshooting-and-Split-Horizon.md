# DNS troubleshooting и split-horizon

## Цель

Отличать проблему записи, resolver path, cache и сетевой доступ и понимать split-horizon design.

## Prerequisites

DNS zones, resolver, TTL, routing и packet capture.

## In scope

Split-horizon DNS возвращает разные ответы внутренним и внешним clients. Проверяйте authoritative answer, recursive cache, source network, search domain, `/etc/resolv.conf` и negative caching.

## Практика

Настройте внутренний и внешний ответ для одного имени, сломайте один resolver и определите, где возникает расхождение.

## Следующие темы

`04-HTTP-TLS-Diagnostics-and-Proxy-Errors.md`, Kubernetes CoreDNS.
