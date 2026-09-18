# Контейнеры: namespaces, cgroups, OCI и runtime

## Что это

Container — обычный Linux process с ограниченным view системы и ресурсов. Это набор kernel primitives, а не маленькая VM.

- namespaces: PID, mount, network, UTS, IPC, user;
- cgroups: CPU, memory, pids, I/O accounting и limits;
- capabilities: разделение полномочий root;
- seccomp: фильтрация system calls;
- rootfs/layers: filesystem view;
- OCI image/runtime specs: общий формат образа и запуска.

## Цепочка runtime

```text
CLI/orchestrator
  ↓
Docker daemon или kubelet
  ↓
containerd / CRI implementation
  ↓
OCI runtime, например runc
  ↓
Linux namespaces/cgroups
```

Docker добавляет daemon, API, build и UX. containerd управляет image/lifecycle primitives. CRI — API Kubernetes к runtime. OCI стандартизирует image/runtime format. Это разные уровни.

## Почему разделяют уровни

Kubernetes должен запрашивать create/start/stop/pull через стабильный CRI и не знать детали runtime. Docker images остаются пригодными, потому что image format совместим с OCI, даже если Docker Engine отсутствует.

## Наблюдение

```bash
unshare --mount --uts --ipc --net --pid --fork sh
lsns
cat /proc/PID/cgroup
cat /proc/PID/status
crictl ps -a
crictl images
ctr -n k8s.io images ls
```

Не проводи destructive experiments на production node. Для диагностики сопоставляй картину `kubectl`, kubelet, CRI, runtime и kernel.

## Отказы

- процесс видит неправильные файлы → mount namespace/rootfs;
- нет DNS → network namespace или DNS;
- OOMKilled → cgroup memory limit или pressure узла;
- image есть в `ctr`, но Pod не стартует → неправильный containerd namespace, auth или manifest;
- root внутри container не равен host root, но capabilities и host mounts могут разрушить границу.

## Вопросы

- **Что изолирует namespace?** Видимость ресурса, но не обязательно сам ресурс.
- **Что ограничивает cgroup?** Использование и limits для дерева процессов.
- **CRI и OCI?** CRI — API orchestrator/runtime, OCI — specification image/runtime.

## Как объяснить за 30–60 секунд

«Контейнер — это process, ограниченный kernel primitives. OCI задаёт image/runtime format, runc выполняет bundle, containerd управляет lifecycle, CRI даёт Kubernetes стабильный API. Docker — более широкий product layer; отсутствие Docker daemon на node не означает невозможность запустить Docker-format image».

[[01-Fundamentals/Linux/Kernel-Processes-Memory]], [[02-Junior/Containers/Docker-Images-Volumes-Networks]], [[03-JuniorPlus/Kubernetes/Core-Architecture-and-Objects]].

## Пространства имён подробнее

PID namespace даёт собственное numbering процессов: процесс может иметь PID 1 внутри container, хотя имеет другой PID на host. Mount namespace задаёт видимое дерево файлов. Network namespace содержит interfaces, route table, sockets и firewall view. User namespace позволяет сопоставить root внутри с непривилегированным UID снаружи.

Namespace изолирует view, но process всё ещё делит kernel и hardware с другими workloads. Cgroup ограничивает collective resource usage, но не превращает process в VM.

## OCI lifecycle

OCI image specification описывает manifest, config и layers. Runtime specification описывает bundle/rootfs и process settings. Runtime создаёт namespaces, применяет cgroups, capabilities и seccomp, затем запускает `exec`.

```text
image manifest → pull/unpack → rootfs
→ runtime config → namespaces/cgroups
→ init process → signals/exit status
```

Если image успешно unpacked, это ещё не значит, что application стартует: entrypoint, permissions, env, ports и dependencies проверяются позже.

## Практическая проверка

```bash
cat /proc/SELF/ns/pid
cat /proc/SELF/ns/net
cat /proc/SELF/cgroup
lsns -p PID
```

Сравни вывод внутри container и на host. Ожидается разный network namespace и cgroup path, но общий kernel version. Это показывает, что container — изолированный process, а не отдельная OS.

## Безопасность

Root внутри container может получить опасные возможности через `--privileged`, host PID/network, hostPath и лишние capabilities. Уменьшай surface через non-root UID, `drop: [ALL]`, read-only root filesystem, seccomp, no-new-privileges и отсутствие Docker socket внутри workload.

## Практическое задание

1. Запусти два container и сравни PID/network/mount namespace.
2. Ограничь memory и CPU через cgroup/runtime.
3. Удали одну capability и проверь, какая операция перестала работать.
4. Создай image без root и сравни UID внутри/снаружи.
5. Сымитируй OOM и докажи источник завершения через runtime и kernel.
