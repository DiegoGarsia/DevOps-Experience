# Admission controllers и policy

## Цель

Понимать, как cluster проверяет и изменяет object до persistence, и проектировать policy без отказа control plane.

## Prerequisites

API Server, RBAC, webhooks, namespaces, security contexts и resource policies.

## In scope

Admission выполняет mutation и validation после authn/authz и до записи object. Webhook может проверять image provenance, required labels, security context, resource requests и allowed registries.

Policy должна иметь timeout, failure behavior, namespace scope, versioning и emergency bypass. Недоступный webhook с fail-closed может блокировать все deployments, fail-open — пропустить нарушение.

## Диагностика

Читайте API response, webhook logs, certificate/CA, service endpoints, timeout и object match rules. Изолируйте policy change на test namespace.

## Практика

Запретите privileged Pod и image из непроверенного registry, проверьте reject/allow paths и сценарий временной недоступности webhook.

## Следующие темы

`12-Helm-Release-Management-and-Templating.md`, supply chain и policy as code.
