# Core и Optional Policy

## Цель

Не дать учебнику превратиться в энциклопедию продуктов. Обязательное ядро изучает переносимые механизмы и один representative tool, а optional раскрывает специализации.

## Что входит в Core

Core должен отвечать на вопрос: «Сможет ли DevOps-инженер применить механизм и диагностировать его отказ в типичной production-системе?»

В Core входят:

- Linux и базовый host operations;
- TCP/IP, DNS, HTTP, TLS, firewall и routing;
- Git;
- KVM/Proxmox как представитель virtualization;
- container model, Docker, containerd, runc и CRI;
- GitLab CI/CD как представитель pipeline platform;
- Terraform и Ansible;
- Kubernetes;
- PostgreSQL;
- Prometheus/Grafana/Alertmanager и log aggregation concepts;
- authentication, authorization, secrets, PKI и supply-chain basics;
- reliability, distributed-system fundamentals и Kafka operational literacy;
- production engineering и troubleshooting.

## Что остаётся Optional

Optional начинается там, где требуется глубокая специализация или конкретный ecosystem product:

- service mesh и Envoy;
- operator development;
- federation implementation;
- eBPF/Cilium internals;
- advanced kernel and TCP tuning;
- Packer;
- конкретный OPA/Gatekeeper implementation;
- Vault HA internals;
- Patroni internals;
- Kafka Streams/Connect и protocol tuning;
- cloud-specific material;
- альтернативные container runtimes;
- deep consensus implementation;
- advanced SRE policy design.

## Правило переноса

Тема остаётся Core, если её отсутствие мешает понять system behavior, failure mode, безопасность или recovery. Тема становится Optional, если после понимания механизма её конкретную реализацию можно освоить на рабочем месте без изменения базовой модели.

## Что запрещено

- Изучать несколько инструментов одного класса вместо понимания механизма.
- Использовать Optional как замену пробелу в Fundamentals.
- Дублировать в Optional материал Core без новой глубины.
