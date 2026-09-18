# Variables, locals, outputs и expressions

## Цель

Научиться отделять входные параметры модуля, внутренние вычисления и публичные результаты.

## Prerequisites

Providers/resources, HCL-like syntax и structured data.

## In scope

Variables задают входной контракт и могут иметь type, default, validation и sensitive flag. Locals именуют повторяемые выражения внутри модуля. Outputs публикуют значения для root module, других modules и automation.

Типы должны быть явными: string, number, bool, list, set, map и object. `null` и отсутствие значения могут по-разному влиять на provider defaults. Expressions поддерживают conditionals, for expressions, functions и references.

```hcl
variable "vm_count" {
  type    = number
  default = 2
  validation {
    condition     = var.vm_count > 0
    error_message = "vm_count must be positive"
  }
}

output "vm_ids" {
  value     = example_vm.app[*].id
  sensitive = false
}
```

## Типичные ошибки

- хранить environment-specific values в module code;
- объявлять sensitive output и печатать его в CI;
- использовать неограниченные `any` types;
- не валидировать диапазон;
- путать `null`, empty и unknown value.

## Практика

Создайте module variables для count, network и image, добавьте validation, outputs и отдельные tfvars для staging.

## Следующие темы

`04-Modules-and-Module-Contracts.md`, dependency graph и lifecycle.
