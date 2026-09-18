# Проектирование Kubernetes RBAC

## Цель

Спроектировать access model для людей, workloads и controllers с минимальным scope и auditability.

## Prerequisites

RBAC basics, ServiceAccounts, namespaces, API groups, Secrets и admission.

## In scope

Начинайте с действий и ресурсов, затем создавайте Role/ClusterRole и bindings. Разделяйте read, deploy, operate и cluster-admin responsibilities. Проверяйте wildcard rules, escalation paths, impersonation и access to Secrets.

## Практика

Опишите RBAC для developer, deployer и monitoring agent, проверьте effective permissions и удалите лишний cluster-wide доступ.

## Следующие темы

`05-Network-Segmentation-and-Security-Policies.md`, security contexts и admission.
