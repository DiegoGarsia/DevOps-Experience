# HTTP/TLS diagnostics и proxy errors

## Цель

Разделять DNS, TCP, TLS, proxy и application причины одной ошибки доступности.

## Prerequisites

HTTP, API, TLS/SNI, certificates, timeouts, proxy и load balancing.

## In scope

Снимайте status, headers, request ID, phase timings и TLS chain. `502` обычно означает ошибку upstream, `503` — отсутствие готового backend/overload, `504` — timeout, но точный смысл зависит от proxy contract.

## Практика

Создайте отдельные DNS failure, expired certificate, wrong SNI, refused upstream и slow upstream. Для каждого определите layer, evidence и следующий тест.

## Следующие темы

HTTP/TLS troubleshooting Production и Kubernetes Ingress.
