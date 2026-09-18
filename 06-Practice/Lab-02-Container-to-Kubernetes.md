# Лаборатория 02: от container к Kubernetes

## Цель

Связать namespaces/cgroups, OCI image, containerd/CRI, Pod, Service и Ingress.

## Шаги

1. Собери multi-stage image с non-root пользователем.
2. Запусти container с volume, network и resource limit.
3. Проверь layers, mount, cgroup и DNS service-name.
4. Разверни Pod/Deployment, Service и ConfigMap.
5. Добавь readiness/liveness/startup probes.
6. Создай намеренный `ImagePullBackOff`, `CrashLoopBackOff` и PVC `Pending`.
7. Восстанови каждую проблему по Events → logs → object relationship.
8. Проверь путь `Ingress → Service → EndpointSlice → Pod`.

## Критерий

Нельзя использовать «перезапусти Pod» как единственный ответ. Для каждой проблемы нужно сохранить evidence, назвать root cause и prevention.

[[02-Junior/Containers/Isolation-OCI-Runtime]], [[03-JuniorPlus/Kubernetes/Core-Architecture-and-Objects]], [[05-Production-Troubleshooting/Kubernetes]].

## Ожидаемые результаты

- `docker history` показывает слои, а `inspect` — mounts/network/limits;
- service name разрешается только внутри общей network;
- `kubectl describe` показывает Events, которые отличают image/config/volume/scheduler failure;
- readiness failure убирает Pod из EndpointSlice, liveness failure вызывает restart;
- исправление одного слоя не должно менять остальные без необходимости.

## Намеренно сломанные сценарии

1. Измени image tag → `ErrImagePull`/`ImagePullBackOff`.
2. Удали ключ из Secret → application start/config failure.
3. Укажи неверный Service selector → endpoints пусты.
4. Сделай PVC без StorageClass → Pending.
5. Поставь memory limit ниже реального потребления → OOMKilled.
6. Убери volumeMount у canary → application behavior differs from stable.

## Definition of Done

Для каждого сценария есть факты из Events/logs/runtime, проверенная hypothesis, минимальный fix, повторный test и prevention: validation, probe, policy, resource setting или test.
