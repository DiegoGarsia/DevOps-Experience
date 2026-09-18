# DevOps Knowledge Base — учебная программа Strong Middle

Это фундаментальный учебник, а не резюме и не каталог инструментов. Главная единица обучения — механизм: что происходит, почему система устроена именно так, какие есть компромиссы и как доказать причину сбоя.

## Как проходить программу

Двигайся сверху вниз. Не переходи к следующему уровню, пока не можешь объяснить предыдущий без привязки к конкретному инструменту.

```text
Fundamentals
  ↓ механизмы Linux, сетей и Git
Junior
  ↓ практическая упаковка и автоматизация
Junior+
  ↓ связи между системами
Middle
  ↓ архитектура, компромиссы и отказоустойчивость
Production / Troubleshooting
  ↓ инциденты, первопричины и предотвращение повторения
```

Для каждой темы используй цикл:

```text
прочитать модель → выполнить лабораторию → сломать один слой
→ собрать факты → объяснить первопричину → исправить → проверить предотвращение
```

## Карта зависимостей

```text
процесс Linux
  ↓
пространства имён / cgroups / файловая система
  ↓
контейнер → OCI-образ → runtime → CRI
  ↓
Pod Kubernetes → контроллеры → Service → Ingress
```

```text
Ethernet → MAC/ARP → IP/подсеть/маршрут → TCP/UDP/порт/socket
  → DNS → HTTP → TLS → reverse proxy → маршрутизация Kubernetes
```

```text
Git → CI pipeline → Runner → сборка → Registry → проверка безопасности
  → deployment → health gates → мониторинг → rollback
```

```text
Terraform → инфраструктура/state → Ansible → конфигурация узла
  → контейнер/Kubernetes → приложение → PostgreSQL/backup
```

## Fundamentals

### Linux

- [[01-Fundamentals/Linux/Kernel-Processes-Memory]]
- [[01-Fundamentals/Linux/Filesystem-Storage]]
- [[01-Fundamentals/Linux/systemd-Users-SSH]]
- [[01-Fundamentals/Linux/Bash]]

### Сети

- [[01-Fundamentals/Networking/Layers-IP-TCP-UDP]]
- [[01-Fundamentals/Networking/DNS]]
- [[01-Fundamentals/Networking/HTTP-TLS]]
- [[01-Fundamentals/Networking/Firewall-NAT-Proxy]]

### Git

- [[01-Fundamentals/Git/Git-Object-Model]]

## Junior

- [[02-Junior/Virtualization/VM-LXC-Proxmox]]
- [[02-Junior/Containers/Isolation-OCI-Runtime]]
- [[02-Junior/Containers/Docker-Images-Volumes-Networks]]
- [[02-Junior/CI-CD/GitLab-Pipeline-Runner]]
- [[02-Junior/IaC/Terraform-Ansible]]

## Junior+

- [[03-JuniorPlus/Kubernetes/Core-Architecture-and-Objects]]
- [[03-JuniorPlus/Kubernetes/Networking]]
- [[03-JuniorPlus/Kubernetes/Storage-Config-Secrets-RBAC]]
- [[03-JuniorPlus/Kubernetes/Workloads-Probes-Scaling]]
- [[03-JuniorPlus/Databases/PostgreSQL-Internals]]
- [[03-JuniorPlus/Observability/Metrics-Logs-Alerts]]

## Middle

- [[04-Middle/Architecture/Delivery-Architecture-and-Tradeoffs]]
- [[04-Middle/Security/Security-Model-and-Secrets]]
- [[04-Middle/Reliability/Availability-Backup-Recovery]]
- [[04-Middle/Messaging/Kafka-Fundamentals-and-Failures]]
- [[04-Middle/Architecture/State-Consistency-and-Scaling]]

## Production / Troubleshooting

- [[05-Production-Troubleshooting/Universal-Method]]
- [[05-Production-Troubleshooting/Linux]]
- [[05-Production-Troubleshooting/Networking]]
- [[05-Production-Troubleshooting/Containers]]
- [[05-Production-Troubleshooting/Kubernetes]]
- [[05-Production-Troubleshooting/CI-CD]]
- [[05-Production-Troubleshooting/Terraform]]
- [[05-Production-Troubleshooting/PostgreSQL]]
- [[05-Production-Troubleshooting/Incident-Response]]

## Практический трек

- [[06-Practice/Lab-01-Linux-and-Networking]]
- [[06-Practice/Lab-02-Container-to-Kubernetes]]
- [[06-Practice/Lab-03-CI-CD-and-Rollback]]
- [[06-Practice/Lab-04-Observability-and-Incident]]

## Границы основного курса

### Включено

В основной контур входят Linux, TCP/IP, DNS, HTTP/TLS, Bash, Git, виртуализация, OCI-контейнеры, Docker как экосистема, containerd/CRI, Kubernetes, GitLab CI/CD, Terraform, Ansible, secrets/RBAC/TLS, PostgreSQL, Kafka на необходимом уровне, Prometheus/Grafana/Alertmanager/Loki и reliability.

Это концепции и representative implementations, которые дают переносимое понимание инфраструктуры и имеют практическую ценность на российском рынке.

### Не включено отдельным основным треком

- десятки альтернатив одного класса: достаточно понять концепцию и одну-две representative implementations;
- cloud-specific сервисы AWS/Azure/GCP/Yandex Cloud: облачные primitives можно изучать после core, но курс не привязан к vendor API;
- редкие CNCF-проекты и enterprise products, не добавляющие новую фундаментальную модель;
- service mesh, eBPF, Crossplane, сложные operators и распределённые storage systems — optional deep dive после базового Kubernetes;
- VictoriaMetrics, Zabbix и ELK не дублируют основной observability-трек: они остаются сравнительными примерами после основного курса.

## Контекст локальных материалов

Исходные документы используются как практические примеры, но не определяют границы программы:

- Current Clarity: single-node K3s, containerd, Traefik, Caddy, GitLab CI/CD, Kaniko, Trivy, Kustomize, Helm, PostgreSQL, Prometheus/Grafana, Loki/Promtail, weighted canary.
- Historical/alternative: K3s master+worker, Argo Rollouts, Argo CD, Patroni, PostgreSQL replication, Docker/Compose, BIND, Nginx, cert-manager, step-ca, mkcert.

Исходные файлы не изменялись:

- [[Практика с опытом/DevOps Cheat Sheet]]
- [[Описание проектов/Trainee/DevOps Experience]]
- [[Описание проектов/Clarity/Experience]]

## Контрольные вопросы после курса

1. Можешь ли ты пройти путь DNS → TCP → TLS → HTTP и назвать точку отказа?
2. Можешь ли объяснить процесс Linux → namespace/cgroup → containerd → CRI → Pod Kubernetes?
3. Можешь ли объяснить связи Deployment → ReplicaSet → Pod и Service → EndpointSlice → Pod?
4. Можешь ли спроектировать build → scan → registry → deploy → verify → rollback?
5. Можешь ли объяснить Terraform state, Ansible idempotency, PostgreSQL WAL/backup/restore и observability?
6. Можешь ли во время outage отличить workaround от permanent fix и сформулировать prevention?

Если ответ требует только списка команд, тема ещё не пройдена на Middle-уровень.
