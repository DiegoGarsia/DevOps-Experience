# Environments, deployments и approvals

## Цель

Понять environment как operational boundary и безопасно связать artifact с target, credentials, ownership и verification.

## Prerequisites

Pipeline, artifacts, variables, secrets и rules.

## In scope

Environment описывает target, его URL/metadata, configuration, access policy и deployment history. Staging должен быть достаточно похож на production, чтобы проверять реальные failure modes.

Approval — не замена автоматическим checks. Он подтверждает, что человек или policy согласовал риск после прохождения автоматических gates. Protected environment ограничивает, кто может deploy и какие credentials доступны.

Deployment должен иметь:

- immutable artifact reference;
- pre-deploy validation;
- migration strategy;
- health verification;
- timeout;
- rollback или forward-fix plan;
- audit trail.

## Типичные ошибки

- production deploy из произвольной ветки;
- staging и production используют разные image build;
- approval не имеет owner и expiration;
- environment secret доступен тестовой job;
- deployment считается успешным до readiness.

## Практика

Создайте staging и protected production environment, добавьте manual approval, deploy по digest и post-deploy health check.

## Следующие темы

`08-Immutable-Artifacts-and-Promotion.md`, deployment strategies и rollback.
