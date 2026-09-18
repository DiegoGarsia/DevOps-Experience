# State, plan, apply и destroy

## Цель

Понять state как mapping между configuration и реальными объектами и безопасно управлять жизненным циклом ресурсов.

## Prerequisites

Providers, resources, modules и dependency graph.

## In scope

State хранит resource instances, attributes, provider metadata и dependencies, необходимые Terraform для сравнения desired и actual state. Он может содержать чувствительные значения, поэтому требует encryption, access control и backup.

`plan` сначала refreshes состояние, затем вычисляет diff. `apply` применяет операции в порядке graph. `destroy` удаляет управляемые ресурсы и опасен для stateful infrastructure; его нельзя использовать вместо анализа drift.

```bash
terraform init
terraform plan -out=tfplan
terraform show tfplan
terraform apply tfplan
```

Plan-файл следует применять в том же контексте, для которого он построен: configuration, variables, provider и state должны совпадать.

## Диагностика

Проверяйте state lineage, refresh errors, planned replacement, dependency order и фактический API result. После apply подтверждайте состояние вне Terraform.

## Типичные ошибки

- хранить state в публичном Git;
- применять старый plan после изменения state;
- удалять state для «починки»;
- запускать destroy без backup и approval;
- считать state источником фактической истины без refresh.

## Практика

Создайте test resources, сохраните plan, измените configuration до apply и исследуйте mismatch, затем выполните controlled destroy.

## Следующие темы

`07-Remote-State-Locking-and-Concurrency.md`, import, drift и migration.
