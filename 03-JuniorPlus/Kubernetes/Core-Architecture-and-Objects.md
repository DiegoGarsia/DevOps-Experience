# Kubernetes: control plane, control loop и объекты

## Зачем Kubernetes

Kubernetes поддерживает desired state для контейнеризированных workloads: replicas, image, config, network и storage. Без него запуск контейнера не даёт scheduler, self-healing, service discovery и декларативный rollout.

## Control plane

```text
kubectl/client
   ↓
API Server ←→ etcd
   ↓
controllers + scheduler
   ↓
Node: kubelet → CRI/runtime → container
```

- API Server — authentication, authorization, validation и единая точка API;
- etcd — strongly consistent key-value storage cluster state;
- scheduler выбирает node для unscheduled Pod по resources, taints, affinity и constraints;
- controllers сравнивают desired и observed state и делают reconciliation;
- kubelet на node приводит назначенные Pods к нужному состоянию через CRI.

## Основные связи

```text
Deployment → ReplicaSet → Pod → container
Service → EndpointSlice → ready Pod
Ingress → controller → Service → Pod
```

Pod — единица scheduling с общим network namespace и volumes. Контроллеры управляют Pod косвенно, чтобы переживать его ephemeral nature.

## Control loop

```text
manifest → API object → persisted state
→ watch/event → controller decision → API mutation
→ kubelet/runtime action → observed status → повторная сверка
```

Declarative object — не shell-команда. Повторное `apply` должно быть безопасным, а status может отставать от desired state.

## Жизненный цикл Pod

Pending включает scheduling и image pull, Running не означает готовность для traffic, Ready определяется readiness probe. Restart policy работает внутри Pod, но новый Pod может иметь другой IP и identity.

## Диагностика

```bash
kubectl get nodes,pods,deploy,rs,svc,endpointslices -A
kubectl get pod POD -o wide
kubectl describe pod POD -n NS
kubectl get events -n NS --sort-by=.lastTimestamp
kubectl logs POD -c CONTAINER --previous
kubectl rollout status deployment/APP -n NS
kubectl auth can-i VERB RESOURCE -n NS
```

Сначала object/events, затем controller, node/runtime, image/config/storage и только потом приложение.

## Отказы

- API недоступен → control plane/credentials/network;
- Pod Pending → scheduler/resources/taints/PVC;
- Pod Running, но traffic нет → readiness/Service selector/EndpointSlice;
- старый Pod не удаляется → termination hook/finalizer/PDB/node;
- API принял manifest, но ничего не меняется → wrong namespace/selector/controller ownership.

## Вопросы

- **Почему Kubernetes не управляет container напрямую?** Нужны Pod lifecycle, scheduling и controller reconciliation.
- **Почему Pod ephemeral?** Node failure, rollout и rescheduling создают новый instance; данные/identity выносят в отдельные abstractions.
- **Зачем etcd?** Сохранять cluster state с консистентностью и watch semantics.

## Как объяснить за 30–60 секунд

«Kubernetes — система control loops. API Server принимает desired state и сохраняет его в etcd, scheduler назначает Pod на node, controllers создают дочерние objects, kubelet через CRI запускает containers и возвращает observed status. Deployment, ReplicaSet, Pod, Service и EndpointSlice — связанные уровни, а не независимый YAML».

[[03-JuniorPlus/Kubernetes/Networking]], [[03-JuniorPlus/Kubernetes/Storage-Config-Secrets-RBAC]], [[03-JuniorPlus/Kubernetes/Workloads-Probes-Scaling]].

## Подробный разбор object ownership

`Deployment` не создаёт container напрямую. Он создаёт ReplicaSet с Pod template. ReplicaSet создаёт Pods, scheduler выбирает node, kubelet через CRI запрашивает runtime, а status возвращается через API Server. Service не владеет Pod lifecycle: он наблюдает labels/readiness и строит EndpointSlice.

Desired state — то, что записано в object spec. Actual state — observed status в cluster. Reconciliation — повторное сравнение этих состояний и действие, возвращающее actual state к desired. Control loop должен быть повторяемым и безопасным к промежуточным ошибкам.

## API Server и etcd

API Server выполняет authentication, authorization, admission и validation до записи. etcd хранит cluster state; потеря quorum влияет на writes и control plane. Уже запущенные workloads могут некоторое время продолжать работать, но новые decisions/scheduling/updates станут недоступны.

## Практическое задание

1. Примени Deployment и наблюдай создание ReplicaSet/Pods через `kubectl get -w`.
2. Измени image и проследи rollout history.
3. Измени label так, чтобы Service потерял endpoints.
4. Измени readiness и объясни разницу Running/Ready.
5. Удали Pod и наблюдай, какой controller создаёт новый.
6. Сломай image tag и собери цепочку API → events → kubelet/runtime.

## Интерпретация команд

```bash
kubectl explain deployment.spec.template.spec
kubectl get deployment APP -o yaml
kubectl get rs,pod -l app=APP -o wide
kubectl get endpointslice -l kubernetes.io/service-name=SERVICE -o yaml
```

`get -o yaml` показывает фактический object, а не файл, который ты хотел применить. EndpointSlice позволяет проверить реальные selected addresses и conditions.
