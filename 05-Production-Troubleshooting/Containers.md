# Docker и containerd troubleshooting guide

## Контейнер завершается

`docker ps -a`, exit code, current/previous logs, command/entrypoint, environment и signal. В Kubernetes: `kubectl describe`, events, `kubectl logs --previous`, затем `crictl` и runtime journal.

## ImagePullBackOff/ErrImagePull

```text
Events
↓
image name/tag/digest/architecture
↓
imagePullSecret/auth
↓
DNS/TCP/TLS/CA
↓
registry manifest/layers
↓
containerd namespace/config
```

`unauthorized` — credentials/scope, `x509` — CA/name/time, timeout — network, `manifest unknown` — path/tag/architecture.

## Volume/network/permission

`inspect`, `network inspect`, `volume inspect`, UID/GID, mount mode и securityContext. Name resolution проверяется из того же network namespace, где работает application. Published port не доказывает правильный internal bind address.

## Prevention

Immutable digest, multi-stage/non-root image, scan gate, minimal capabilities, resource limits, healthchecks, registry availability and documented rollback.

[[05-Production-Troubleshooting/Universal-Method]], [[02-Junior/Containers/Isolation-OCI-Runtime]], [[02-Junior/Containers/Docker-Images-Volumes-Networks]].

## Сценарий: Pod `ImagePullBackOff`

1. `describe pod` и Events: exact error.
2. Проверить image name/tag/digest и architecture.
3. Проверить Secret в том же namespace и его registry scope.
4. Проверить DNS/TCP/TLS/clock/CA с node.
5. Проверить containerd namespace/config и runtime logs.
6. Исправить одну причину, дождаться нового pull и проверить digest.

## Сценарий: контейнер OOMKilled

Сравни exit code, runtime state, cgroup memory events, node pressure и kernel journal. Если application сама завершилась, logs/exit code отличаются от kernel OOM. Prevention: измеренные requests/limits, memory metrics, load test и graceful degradation.

## Сценарий: service name не работает в Compose

Проверь, что containers в одной network, имя — service name, DNS отвечает внутри network namespace, application слушает правильный bind address, а не только localhost. `ports` нужен для доступа извне и не влияет на внутреннее имя.
