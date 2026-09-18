# ServiceAccounts и RBAC basics

## Цель

Понять identity workload и минимальные разрешения Kubernetes API.

## Prerequisites

Authentication/authorization, namespaces, API objects и Secrets.

## In scope

ServiceAccount — identity Pod для обращения к API или внешнему provider. RBAC состоит из Role/ClusterRole, RoleBinding/ClusterRoleBinding и verb/resource/apiGroup. Role namespaced, ClusterRole может быть cluster-scoped или привязан к namespace.

По умолчанию создавайте отдельный ServiceAccount и отключайте automount token, если API access не нужен. Разрешения должны быть минимальными и проверяться через audit.

## Диагностика

Сопоставляйте Pod serviceAccountName, binding subject, role rules и request resource/verb. `403` означает authorization denial, а не network failure.

## Типичные ошибки

- использовать default ServiceAccount;
- дать `cluster-admin` для чтения одного ConfigMap;
- сделать ClusterRoleBinding вместо namespaced RoleBinding;
- забыть rotation/token projection;
- проверять доступ не от identity Pod.

## Практика

Создайте ServiceAccount только для чтения ConfigMap в одном namespace, проверьте allowed/denied API calls и удалите лишнее разрешение.

## Следующие темы

`17-PV-and-PVC.md`, security contexts и Middle RBAC design.
