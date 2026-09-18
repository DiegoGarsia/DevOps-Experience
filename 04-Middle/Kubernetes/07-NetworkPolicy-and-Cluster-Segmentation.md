# NetworkPolicy и cluster segmentation

## Цель

Ограничить traffic по принципу default deny и не потерять необходимые DNS, ingress, egress и service flows.

## Prerequisites

CNI, labels/selectors, Services, DNS, TCP и firewall model.

## In scope

NetworkPolicy задаёт ingress/egress правила для Pods по namespace, Pod labels, IP blocks и ports. Политика действует только если CNI её поддерживает. После выбора Pod хотя бы одной policy соответствующее направление может стать isolated.

Безопасная стратегия: сначала описать allowed flows, добавить DNS/observability/management exceptions, затем включить default deny и проверять egress отдельно.

## Диагностика

Проверяйте policy selection, source/destination labels, namespace selectors, port/protocol, CNI enforcement и capture. Тестируйте traffic из реального Pod, а не с node.

## Типичные ошибки

- default deny ломает DNS;
- policy допускает ingress, но забывает egress;
- namespace selector выбран слишком широко;
- CNI не поддерживает нужное поле;
- считать NetworkPolicy заменой identity/RBAC.

## Практика

Разделите frontend/backend/database, включите default deny, разрешите нужные направления и намеренно проверьте DNS и egress failure.

## Следующие темы

`08-CSI-and-Volume-Lifecycle.md`, security boundaries и troubleshooting.
