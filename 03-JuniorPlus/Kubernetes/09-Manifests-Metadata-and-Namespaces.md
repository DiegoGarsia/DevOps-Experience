# Manifests, metadata и namespaces

## Цель

Научиться организовывать objects в namespace и использовать labels/selectors для устойчивой связи компонентов.

## Prerequisites

API objects, YAML, labels, selectors и RBAC basics.

## In scope

Namespace задаёт logical scope и часть security/resource boundaries. `metadata.name` уникален в пределах namespace, labels предназначены для selection, annotations — для metadata, которую consumers не используют как selector.

Selectors связывают Deployment с Pods, Service с endpoints и policy с workloads. Изменение selector после создания может быть запрещено или привести к потере ownership.

Manifest должен быть versioned, иметь явный namespace и не содержать лишних generated fields. Labels должны описывать identity, version и ownership, а не временное состояние.

## Типичные ошибки

- selector не совпадает с labels;
- использовать annotation вместо label для selection;
- deploy в `default` без policy;
- смешивать namespaced и cluster-scoped objects;
- хранить generated status в Git.

## Практика

Создайте два namespace и два workload с похожими labels, затем проверьте, какой Service выбирает какие Pods и как RBAC ограничивает видимость.

## Следующие темы

`10-Pods-and-Pod-Lifecycle.md`, Deployments и Services.
