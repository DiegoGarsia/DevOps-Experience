# Identity, least privilege и RBAC

## Цель

Научиться выдавать минимальные permissions субъекту для конкретной задачи и контролировать их lifecycle.

## Prerequisites

Authentication/authorization, Linux permissions, ServiceAccounts и API resources.

## In scope

RBAC связывает subject, role, resource, verb и scope. Least privilege означает отсутствие неиспользуемых прав, ограничение namespace/resource и регулярный review.

Разрешение должно быть измеримо: read ConfigMap в namespace не превращается в cluster-admin. Для automation создавайте отдельный identity и короткий credential lifetime.

## Диагностика

Составляйте effective permission graph и проверяйте действие от имени реального subject. Ищите transitive group membership, inherited roles и stale credentials.

## Типичные ошибки

- wildcard verbs/resources;
- cluster-wide binding для namespaced задачи;
- права без owner/expiry;
- неотозванный доступ бывшего сервиса;
- проверка role definition без binding.

## Практика

Спроектируйте две роли для app и deployer, проверьте разрешённые/запрещённые действия и отзовите одну binding.

## Следующие темы

`03-Secrets-and-Secret-Lifecycle.md`, Kubernetes RBAC и security boundaries.
