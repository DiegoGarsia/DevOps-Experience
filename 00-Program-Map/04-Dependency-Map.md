# Dependency Map

## Цель

Показать не список инструментов, а причинно-следственные связи между механизмами и operational decisions.

## Host и runtime

```text
CPU and memory
    ↓
Processes and threads
    ↓
File descriptors and I/O
    ↓
Namespaces
    ↓
cgroups
    ↓
OCI runtime
    ↓
containerd
    ↓
CRI
    ↓
Kubelet
    ↓
Pod
```

## Network request

```text
Ethernet
    ↓
ARP
    ↓
IP and route
    ↓
TCP connection
    ↓
DNS lookup
    ↓
HTTP request
    ↓
TLS validation
    ↓
Proxy
    ↓
Load balancer
    ↓
Ingress
    ↓
Service
    ↓
EndpointSlice
    ↓
Pod
```

## Delivery

```text
Commit
    ↓
Pipeline
    ↓
Test
    ↓
Immutable artifact
    ↓
Registry
    ↓
Environment promotion
    ↓
Deployment
    ↓
Health verification
    ↓
Canary or rolling rollout
    ↓
Rollback if signal is unsafe
```

## Data и recovery

```text
Transaction
    ↓
WAL
    ↓
Checkpoint
    ↓
Backup
    ↓
Restore validation
    ↓
PITR
    ↓
RPO/RTO
    ↓
Recovery exercise
```

## Observability и incident

```text
Metric, log, trace
    ↓
Signal quality
    ↓
Alert
    ↓
SLI/SLO
    ↓
Incident scope
    ↓
Hypothesis
    ↓
Evidence
    ↓
Fix
    ↓
Verification
    ↓
Prevention
```

## Принцип чтения графа

Стрелка означает prerequisite, а не обязательное использование конкретного инструмента. Например, понимание TCP необходимо для диагностики Kubernetes Service, но не требует изучать конкретный CNI до освоения самого TCP.
