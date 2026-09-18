# Dependency security, SBOM и vulnerabilities

## Цель

Понимать состав software artifact и управлять уязвимостями через risk-based remediation.

## Prerequisites

Package management, Docker images, CI/CD, registries и basic threat modeling.

## In scope

SBOM описывает компоненты artifact: package, version, source и relationship. Scanner сопоставляет их с vulnerability database, но finding требует контекста: reachable ли код, есть ли exploit, exposure, compensating control и upgrade path.

Dependency policy должна фиксировать lock/checksum, approved registries, patch SLA, exception owner и expiry. Нельзя автоматически блокировать каждый finding без оценки false positives и availability.

## Диагностика

Сопоставляйте image digest, SBOM version, scan database freshness и deployed artifact. После remediation проверяйте, что исправленный digest действительно продвинут.

## Типичные ошибки

- сканировать tag вместо digest;
- считать CVSS единственным risk signal;
- не обновлять vulnerability database;
- exception без expiry;
- исправить source, но оставить старый image в registry/deployment.

## Практика

Соберите SBOM для image, классифицируйте findings, исправьте одну dependency и подтвердите новый digest в deployment.

## Следующие темы

`07-Threat-Modeling-and-Security-Boundaries.md`, supply-chain security и CI policy.
