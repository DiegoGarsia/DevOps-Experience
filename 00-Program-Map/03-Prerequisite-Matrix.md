# Prerequisite Matrix

## Цель

Сделать зависимости обучения явными и не заставлять учащегося изучать инструмент раньше механизма, который он скрывает.

## Матрица

| Область             | Что нужно знать заранее                           | К чему ведёт                                  |
| ------------------- | ------------------------------------------------- | --------------------------------------------- |
| Linux               | Компьютерная модель и CLI                         | Storage, systemd, containers, troubleshooting |
| Networking          | Компьютерная модель и базовый CLI                 | DNS, HTTP, TLS, proxy, Kubernetes networking  |
| Git                 | Файлы, каталоги и текстовые данные                | CI/CD, IaC и promotion                        |
| Virtualization      | Linux storage, процессы и сеть                    | VM, LXC, Proxmox и инфраструктура             |
| Containers          | Processes, namespaces, cgroups, filesystem, сеть  | Docker, containerd и Kubernetes               |
| Docker              | Container model, images, runtime, сеть и storage  | Registry, CI/CD и deployment                  |
| CI/CD               | Git, artifacts, images и базовая автоматизация    | Promotion, deployment, rollback               |
| Terraform           | HCL-подобная декларативная модель, ресурсы и сеть | Reusable infrastructure и drift management    |
| Ansible             | Linux, SSH, YAML и desired state                  | Configuration management и host operations    |
| Kubernetes          | Containers, networking, storage, Git и CI/CD      | Cluster operations, scheduling, recovery      |
| PostgreSQL          | Linux, storage, network, SQL и transactions       | Replication, HA, PITR и performance           |
| Observability       | Processes, network, logs и базовая статистика     | Prometheus, alerts, SLI/SLO и investigation   |
| Security            | Linux identity, network, TLS, containers и Git    | RBAC, secrets, supply chain и threat modeling |
| Reliability         | Все базовые operating skills                      | RPO/RTO, DR, capacity и resilience            |
| Distributed Systems | Network, storage, replication и failure modes     | Kafka, consistency и architecture             |
| Production          | Все предыдущие уровни                             | Safe change, incident response и ownership    |

## Критическая цепочка

```text
Processes
  ↓
Namespaces and cgroups
  ↓
Containers
  ↓
containerd and CRI
  ↓
Kubernetes
```

```text
Ethernet
  ↓
IP and routing
  ↓
TCP and sockets
  ↓
DNS
  ↓
HTTP
  ↓
TLS
  ↓
Proxy and load balancing
  ↓
Kubernetes networking
```

```text
Git
  ↓
CI/CD
  ↓
Image and registry
  ↓
Deployment
  ↓
Rollout
  ↓
Verification and rollback
```

## Как использовать матрицу

Если prerequisites неизвестны, сначала возвращайтесь к фундаментальному файлу, а не компенсируйте пробел копированием готовой конфигурации. Иначе ошибка будет устранена только на поверхности и проявится на следующем уровне.
