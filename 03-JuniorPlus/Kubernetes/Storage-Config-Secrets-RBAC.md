# Kubernetes: storage, configuration и RBAC

## Storage

```text
Container filesystem
↓
Volume
↓
PVC
↓
PV
↓
StorageClass/provisioner
↓
actual storage
```

`emptyDir` живёт вместе с Pod, `hostPath` связывает workload с node и опасен для переносимости. PVC — запрос приложения, PV — предоставленный ресурс, StorageClass описывает dynamic provisioning. Access modes задают допустимое подключение, reclaim policy — судьбу PV после удаления claim.

StatefulSet связывает stable identity и volume claims с ordinal Pod. Это не делает database автоматически HA.

## ConfigMap и Secret

ConfigMap хранит non-sensitive configuration. Secret — API object для sensitive data, но base64 не является encryption. Секреты должны защищаться RBAC, encryption at rest, audit и rotation.

Проверяй namespace, имя объекта, key, `env`, `envFrom`, volume и restart после изменения.

## RBAC

```text
ServiceAccount → Role/ClusterRole → RoleBinding/ClusterRoleBinding → verbs/resources
```

Role namespaced, ClusterRole может быть cluster-wide. `get/list/watch/create/update/delete/patch` — разные полномочия. Least privilege важнее удобства cluster-admin.

```bash
kubectl get pvc,pv,storageclass -n NS
kubectl describe pvc CLAIM -n NS
kubectl get configmap,secret -n NS
kubectl auth can-i get pods --as=system:serviceaccount:NS:SA -n NS
kubectl describe pod POD -n NS
```

## Отказы

- PVC Pending → StorageClass, provisioner, capacity, topology, access mode и events;
- mount failure → path, UID/GID, filesystem и securityContext;
- потеря данных → неправильный reclaim/delete, отсутствие backup/restore;
- Forbidden → ServiceAccount, Role и Binding;
- Secret не найден → namespace/name/key или permission;
- hostPath повредил portability → заменить на managed persistent storage.

## Вопросы

- **PVC и PV?** PVC — потребительский запрос, PV — предоставленный ресурс.
- **Почему Secret не равен шифрованию?** Base64 только encoding; нужны encryption at rest и контроль доступа.
- **Зачем RBAC?** Ограничить blast radius identity, например CI Runner.

## Как объяснить за 30–60 секунд

«Storage имеет отдельный lifecycle: Pod ephemeral, PVC выражает потребность, PV представляет ресурс, StorageClass автоматизирует выдачу. ConfigMap и Secret передают configuration, RBAC ограничивает API actions. StatefulSet даёт identity и claims, но не заменяет backup и HA».

[[03-JuniorPlus/Kubernetes/Core-Architecture-and-Objects]], [[04-Middle/Security/Security-Model-and-Secrets]], [[04-Middle/Reliability/Availability-Backup-Recovery]].

## Жизненный цикл PVC

```text
PVC created
↓
StorageClass/provisioner
↓
PV bound
↓
Pod scheduled
↓
volume attached/mounted
↓
application reads/writes
↓
Pod deleted or rescheduled
```

`Pending` означает, что claim ещё не получил usable PV; это не всегда проблема Pod. `Bound` не гарантирует успешный mount: attach, filesystem, permissions и topology проверяются позже.

## Secret lifecycle

Secret появляется через API, затем попадает в environment или mounted volume. Environment часто остаётся в process inspection и может попасть в crash dump; volume имеет свой permission model. Rotation требует обновить source, rollout/reload consumer и проверить, что старый credential отозван.

## RBAC workflow

```bash
kubectl auth can-i get pods -n NS --as=system:serviceaccount:NS:SA
kubectl auth can-i create deployments -n NS --as=system:serviceaccount:NS:SA
kubectl get role,rolebinding -n NS
kubectl describe rolebinding NAME -n NS
```

Если `Forbidden`, сравни subject, namespace, resource, verb и API group. Не выдавай ClusterRoleBinding только для устранения одного namespace error.

## Практическое задание

1. Создай PVC и наблюдай transitions Pending/Bound.
2. Сделай неверный StorageClass и прочитай events provisioner.
3. Создай volume с неправильным UID/GID и исправь securityContext.
4. Передай configuration через ConfigMap и Secret, измени key и изучи симптом.
5. Создай ServiceAccount с одной разрешённой операцией и проверь `auth can-i`.
6. Удали StatefulSet отдельно от PVC и объясни data lifecycle.
