# Threat modeling и security boundaries

## Цель

Научиться находить assets, trust boundaries, threat actors и compensating controls до внедрения системы.

## Prerequisites

Authentication/authorization, network path, containers, CI/CD, secrets и observability.

## In scope

Начинайте с data flow: user, DNS, proxy, application, database, registry, runner и cluster. Для каждой границы задайте: кто доверяет кому, какие credentials пересекают boundary, какие операции разрешены и как обнаружить злоупотребление.

Риск складывается из exposure, likelihood и impact. Control может быть preventive, detective или corrective. Защита должна включать least privilege, segmentation, validation, signing, audit, rotation и recovery.

## Диагностика

Проверяйте фактический path и effective permissions, а не только design document. Неизвестный outbound access, privileged runner и общий credential — признаки неявной trust boundary.

## Типичные ошибки

- threat model только для внешнего злоумышленника;
- доверять internal network без authentication;
- считать scanner заменой design control;
- не моделировать ошибочного оператора и compromised dependency;
- нет detection/recovery для accepted risk.

## Практика

Нарисуйте data-flow для CI → registry → Kubernetes → PostgreSQL, отметьте пять trust boundaries и назначьте preventive/detective controls.

## Следующие темы

Middle security operations, supply chain, network policies и incident response.
