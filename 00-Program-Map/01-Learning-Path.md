# Learning Path

## Цель

Показать рекомендуемый порядок прохождения базы так, чтобы каждая следующая область опиралась на уже изученные механизмы.

## Маршрут

### 1. Fundamentals

Изучаются компьютерная модель, Linux, файловая система, процессы, память, Bash, SSH, сеть, DNS, HTTP, TLS и Git. Результат — способность уверенно работать с одним Linux-хостом и объяснять путь сетевого запроса.

### 2. Junior

Добавляются package management, boot, storage, `systemd`, виртуализация, namespaces, cgroups, OCI, containerd, Docker, CI/CD, Terraform и Ansible. Результат — воспроизводимая подготовка хоста, сборка артефакта и базовая автоматизация.

### 3. JuniorPlus

Изучаются control plane Kubernetes, API objects, Pods, workloads, Service discovery, storage, resources, PostgreSQL, Prometheus, Grafana и базовая security model. Результат — способность собрать и диагностировать небольшой delivery path.

### 4. Middle

Изучаются Kubernetes networking, storage, scheduling, autoscaling, upgrades, PostgreSQL replication and PITR, observability operations, reliability, distributed systems и Kafka. Результат — эксплуатация составной системы и анализ взаимодействия компонентов.

### 5. StrongMiddle

Изучаются ограничения архитектур, performance, data protection, multi-cluster decisions, security boundaries, consistency, quorum, overload control и design review. Результат — самостоятельный выбор решения с явными trade-offs.

### 6. Production

Изучаются production readiness, release safety, SLO, incident response, RCA, runbooks, capacity, DR и cross-domain troubleshooting. Результат — безопасная эксплуатация и восстановление системы.

### 7. Practice

Лаборатории выполняются рядом с теорией. Сложность растёт по цепочке:

```text
guided
    ↓
independent
    ↓
failure injection
    ↓
troubleshooting
    ↓
recovery
    ↓
verification
    ↓
postmortem
```

## Правило перехода

Переход к следующему уровню разрешён, если учащийся может без подсказки:

- объяснить механизм своими словами;
- выполнить базовую операцию;
- собрать факты при отказе;
- отличить симптом от причины;
- безопасно проверить исправление.

## Out of scope

Не требуется проходить Optional до завершения Core. Дополнительные продукты не должны нарушать порядок фундаментальных зависимостей.
