# Kubernetes troubleshooting guide

## Общая последовательность

```text
object/status
↓
events
↓
controller/scheduler/node
↓
image/config/secret/volume/resources
↓
logs/probes
↓
Service/EndpointSlice/Ingress
↓
user path
```

## Состояния

- Pending → scheduler, resources, taints, PVC, events;
- CrashLoopBackOff → exit code, logs/previous logs, config, probes, volume, limits;
- ImagePullBackOff → tag/path, secret, network, TLS/CA, manifest;
- OOMKilled → container limit, node pressure, memory behavior;
- Service без endpoints → selector, labels, readiness, namespace;
- Ingress 404/502 → Host/path, controller, Service/EndpointSlice, upstream;
- DNS failure → Pod resolver, CoreDNS, NetworkPolicy и Service record;
- node NotReady → kubelet, runtime, network, disk pressure, conditions;
- rollout failure → ReplicaSets, readiness, PDB, capacity, compatibility.

## Команды

```bash
kubectl get pod,deploy,rs,svc,endpointslices -A
kubectl describe pod POD -n NS
kubectl get events -n NS --sort-by=.lastTimestamp
kubectl logs POD -c CONTAINER --previous -n NS
kubectl get node -o wide
kubectl describe node NODE
kubectl auth can-i VERB RESOURCE -n NS
```

Не начинай с удаления namespace или restart всех Pods: это уничтожает evidence и может увеличить outage.

## Prevention

Readiness/startup probes, resource requests/limits, PodDisruptionBudget, NetworkPolicy, image digest, RBAC least privilege, rollout gates, backup/restore и alert на user impact.

[[05-Production-Troubleshooting/Universal-Method]], [[03-JuniorPlus/Kubernetes/Core-Architecture-and-Objects]], [[03-JuniorPlus/Kubernetes/Networking]].

## Сценарий: `Service has no endpoints`

```text
kubectl get svc
↓
selector Service
↓
labels Pod
↓
readiness/conditions
↓
kubectl get endpointslice
↓
port/targetPort
```

Если labels не совпадают, controller не выберет Pod. Если Pod не Ready, endpoint может быть исключён. Если endpoint существует, но connection refused, проверяй targetPort и bind address внутри container.

## Сценарий: `Pending` PVC

Проверь `kubectl describe pvc`, events, StorageClass/provisioner, available capacity, access mode, topology и node constraints. Не удаляй claim как «исправление»: это может уничтожить data lifecycle.

## Сценарий: rollout failure

Сохрани ReplicaSets/events/logs, проверь image digest, probes, resource scheduling, PDB и compatibility. Если user impact растёт — останови promotion и верни known-good version. После rollback проверь endpoints и user path.
