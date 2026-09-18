# Проектирование modules и environments

## Цель

Строить reusable modules и изолированные environments без копирования конфигурации и смешения state.

## Prerequisites

Terraform resources, variables, state, modules и remote backend.

## In scope

Environment pattern должен явно отделять backend/state, variables, credentials и deployment policy. Module скрывает повторяемую реализацию, но публикует типизированный contract и outputs.

Разделяйте общие defaults и environment-specific values. Данные production не должны случайно попасть в plan staging. Переиспользование не должно скрывать разные failure domains или backup requirements.

## Практика

Создайте module VM/network и два environment с разными backend/state, затем сравните plan и permissions.

## Следующие темы

`02-State-Refactoring-Migration-and-Policy.md`, drift и plan review.
