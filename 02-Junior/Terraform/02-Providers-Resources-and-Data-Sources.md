# Providers, resources и data sources

## Цель

Различать управление ресурсом и чтение внешнего факта и понимать, как provider связывает Terraform с API платформы.

## Prerequisites

Declarative workflow, API fundamentals, virtualization или другой target platform.

## In scope

Provider реализует schema и операции API конкретной платформы. Resource описывает объект, которым Terraform владеет lifecycle. Data source читает существующее значение и обычно не создаёт его.

```hcl
data "example_image" "ubuntu" {
  name = "ubuntu-24.04"
}

resource "example_vm" "app" {
  image_id = data.example_image.ubuntu.id
  name     = "app-01"
}
```

Provider credentials должны поступать из безопасного environment или secret backend. Версии provider фиксируются в lock file, чтобы разные runner не применяли несовместимую схему.

## Диагностика

Проверяйте provider version, authentication, API endpoint, schema и permissions. Ошибка refresh может быть сетевой, credential-related или следствием удаления объекта вне Terraform.

## Типичные ошибки

- использовать data source там, где нужен managed resource;
- не фиксировать provider version;
- передавать секрет в configuration/state;
- разрешать provider больше API permissions, чем нужно;
- не учитывать eventual consistency API.

## Практика

Получите image/network через data sources, создайте test VM resource и намеренно отзовите API permission, чтобы отличить provider auth от resource error.

## Следующие темы

`03-Variables-Locals-Outputs-and-Expressions.md`, modules и dependency graph.
