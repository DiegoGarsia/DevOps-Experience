# Kubernetes: workloads, probes и scaling

## Workload controllers

- Deployment управляет stateless ReplicaSets и rollout;
- ReplicaSet поддерживает число Pod по selector;
- StatefulSet даёт ordinal identity и volume claims;
- DaemonSet создаёт Pod на подходящих nodes;
- Job выполняет конечную работу до success;
- CronJob создаёт Jobs по расписанию.

Controller не «запускает процесс сам»: он меняет API state, затем scheduler/kubelet/runtime выполняют работу.

## Probes

- `startupProbe` даёт медленному приложению завершить инициализацию;
- `readinessProbe` решает, получает ли Pod traffic;
- `livenessProbe` решает, нужен ли restart.

Неправильная liveness probe создаёт restart storm. Неправильная readiness скрывает рабочий Pod от Service. Probe должна быть дешёвой, детерминированной и проверять подходящий dependency boundary.

## Resources

Requests участвуют в scheduling, limits ограничивают runtime. CPU limit может вызвать throttling, memory limit — OOMKill. Без requests/limits noisy neighbor затрудняет capacity planning.

## Scaling

HPA меняет replicas по metrics API; scaling не исправляет slow dependency, lock contention или single-thread bottleneck. PDB ограничивает добровольные disruptions, но не спасает от hard node failure.

## Rollout

```text
new Pod template
↓
new ReplicaSet
↓
readiness
↓
maxSurge/maxUnavailable
↓
old ReplicaSet scale down
```

Безопасность rollout зависит от compatibility, probes, PDB, capacity и rollback revision.

## Диагностика

```bash
kubectl get deploy,rs,pod -n NS
kubectl rollout status deploy/APP -n NS
kubectl rollout history deploy/APP -n NS
kubectl describe pod POD -n NS
kubectl top pod -n NS
kubectl get hpa,pdb -n NS
```

## Вопросы

- **Running и Ready?** Running означает lifecycle state, Ready — разрешение на traffic.
- **Почему HPA не лечит latency?** Причина может быть downstream, lock или I/O, а не число replicas.
- **Почему новый Pod не стартует при rollout?** Недостаток resources, image/config/volume, probe или scheduling constraints.

## Как объяснить за 30–60 секунд

«Workload controller поддерживает desired count и rollout, но application availability определяется readiness и dependency health. Requests влияют на scheduling, limits — на runtime, HPA — на replicas, PDB — на voluntary disruption. При rollout я проверяю ReplicaSets, events, probes, resources и возможность rollback».

[[03-JuniorPlus/Kubernetes/Core-Architecture-and-Objects]], [[03-JuniorPlus/Kubernetes/Networking]], [[05-Production-Troubleshooting/Kubernetes]].

## Probe как контракт

Probe command/HTTP request выполняется kubelet. `startupProbe` временно отключает влияние liveness/readiness до успешного старта. Readiness failure убирает Pod из traffic endpoints, но не обязательно перезапускает его. Liveness failure приводит к restart и может создать restart storm.

Probe должна быть:

- дешёвой;
- детерминированной;
- ограниченной timeout/failure threshold;
- связанной с тем, что именно нужно доказать.

Не включай недоступность необязательной внешней database в liveness: временный dependency failure тогда превращается в массовые restarts.

## Requests/limits и scheduling

Scheduler использует requests для fit decision. Limits применяются runtime: CPU может throttling, memory может OOMKill. Если requests слишком малы, node overcommitted; если слишком велики, Pod остаётся Pending даже при фактическом свободном CPU.

## Rolling update

`maxSurge` разрешает временные дополнительные Pods, `maxUnavailable` ограничивает доступную потерю. PDB влияет на voluntary disruption, но не гарантирует защиту от node crash. Backward-compatible schema/API важны, когда старая и новая версия работают одновременно.

## Практическое задание

1. Создай приложение с медленным стартом и настрой startup probe.
2. Сделай readiness failure и проверь EndpointSlice.
3. Сделай liveness всегда false и наблюдай restart/backoff.
4. Ограничь memory и сравни OOMKilled с application exit.
5. Измени requests так, чтобы Pod стал Pending.
6. Настрой rollout с maxSurge/maxUnavailable и выполни rollback.
