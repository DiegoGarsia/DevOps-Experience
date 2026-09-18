# Dependency graph и lifecycle

## Цель

Понимать, почему Terraform создаёт ресурсы в определённом порядке и как lifecycle meta-arguments влияют на risk.

## Prerequisites

Resources, data sources, modules и references.

## In scope

Terraform строит graph по references и explicit `depends_on`. Implicit dependency обычно лучше: она отражает реальное потребление output. `create_before_destroy`, `prevent_destroy`, `ignore_changes` и replacement triggers меняют lifecycle, но могут скрыть drift или увеличить capacity requirement.

```text
network
  ↓ id reference
subnet
  ↓
VM
  ↓
service configuration
```

Если attribute immutable в provider, изменение приводит к replacement. Перед apply нужно прочитать plan и проверить, не уничтожит ли изменение stateful resource.

## Диагностика

```bash
terraform graph
terraform plan
terraform show
```

Сопоставляйте graph, plan и реальный API dependency. `depends_on` исправляет ordering, но не делает систему готовой и не заменяет health check.

## Типичные ошибки

- `depends_on` на весь module без причины;
- `ignore_changes` скрывает drift;
- `prevent_destroy` блокирует необходимую миграцию;
- replacement stateful resource без backup;
- считать правильный graph гарантией application readiness.

## Практика

Создайте network → VM → service graph, измените immutable attribute, исследуйте replacement plan и добавьте защиту stateful ресурса.

## Следующие темы

`06-State-Plan-Apply-and-Destroy.md`, remote state и lifecycle operations.
