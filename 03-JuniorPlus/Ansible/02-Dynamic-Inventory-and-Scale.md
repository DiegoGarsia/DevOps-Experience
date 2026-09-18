# Dynamic inventory и scale

## Цель

Получать актуальные groups/hosts из API или CMDB и контролировать concurrency, credentials и blast radius.

## Prerequisites

Inventory, roles, delegation, serial execution и API authentication.

## In scope

Dynamic inventory должна выдавать стабильные host identity, groups, variables и cache policy. При недоступности источника нужно определить fail-closed/fail-open behavior. Большой fleet требует batching, rate limits, retries и audit.

## Практика

Создайте inventory plugin/script для test API, добавьте cache, два groups и serial rollout с остановкой после failure одного batch.

## Следующие темы

Ansible testing, CI/CD и Production automation troubleshooting.
