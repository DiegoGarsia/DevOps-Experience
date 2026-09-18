# Refactoring state, migration и policy

## Цель

Менять структуру Terraform без уничтожения существующих объектов и добавлять policy до применения опасного plan.

## Prerequisites

State, import/drift, modules, locking и dependency graph.

## In scope

Refactoring должен разделять изменение адреса resource и изменение самого объекта. Перед migration нужен backup state, lock, plan и понятный rollback. Policy может запрещать public exposure, unencrypted storage, missing tags или destructive replacement.

Policy не должна быть единственным контролем: нужен review plan и post-apply verification.

## Практика

Переместите resource в module без recreation, добавьте policy для опасного network rule и проверьте отказ plan до apply.

## Следующие темы

`03-Plan-Review-Testing-and-Drift-Operations.md` и Production IaC troubleshooting.
