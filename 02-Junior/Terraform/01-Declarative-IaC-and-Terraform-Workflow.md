# Декларативная IaC-модель и workflow Terraform

## Цель

Понять desired state, plan/apply cycle и ответственность Terraform за инфраструктурные ресурсы.

## Prerequisites

Linux, networking, virtualization, structured configuration и Git.

## In scope

Declarative configuration описывает желаемое состояние, а Terraform сравнивает его с state и данными provider. `plan` показывает proposed changes, `apply` применяет их после подтверждения, `destroy` удаляет управляемые ресурсы.

```text
configuration + variables
        ↓ provider refresh
state + real infrastructure
        ↓ diff
plan
        ↓ approval
apply
```

Terraform не является configuration management для каждого файла ОС. Он создаёт и связывает resources, а Ansible или image pipeline может настраивать host.

## Безопасный workflow

1. форматировать и валидировать код;
2. получить plan в контролируемом окружении;
3. review plan;
4. сохранить plan или зафиксировать его эквивалентность;
5. применить с lock;
6. проверить фактическое состояние.

## Типичные ошибки

- запускать apply без plan review;
- хранить state в Git;
- использовать `destroy` как исправление drift;
- смешивать несколько environments без границ;
- вручную менять ресурсы без понимания последующего drift.

## Практика

Опишите test VM и network, выполните `init`, `validate`, `plan`, примените изменение и проверьте ресурсы вне Terraform.

## Следующие темы

`02-Providers-Resources-and-Data-Sources.md`, variables и state.
