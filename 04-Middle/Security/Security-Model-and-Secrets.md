# Security model и secrets

## Базовая модель

```text
authentication → identity → authorization → action → audit
```

Authentication доказывает, кто субъект. Authorization решает, что ему разрешено. Least privilege уменьшает blast radius. Secret — чувствительное значение; token — credential с правами/TTL; certificate доказывает identity; private key должен оставаться секретным.

## Шифрование

Encryption защищает confidentiality, hashing — проверку/целостность, signature — authenticity/integrity. TLS даёт channel protection, CA chain — доверие identity. Secret manager хранит/выдаёт secrets, но не исправляет избыточные permissions.

## Kubernetes и CI

- ServiceAccount + RBAC ограничивают API;
- Secret требует encryption at rest, namespace isolation, audit и rotation;
- Registry credentials имеют scope, например pull only;
- Runner получает минимальные verbs/resources;
- image/dependency/secret scanning — gates до deploy;
- non-root, read-only filesystem, drop capabilities и NetworkPolicy уменьшают runtime risk.

HashiCorp Vault — одна из реализаций secret manager: он хранит версии secrets, выдаёт их по policy и может использовать TTL/dynamic credentials. Kubernetes Secret и Vault — разные уровни; интеграция не отменяет RBAC и rotation.

## Threats

- secret в Git/image/logs → rotation и history purge;
- privileged container/hostPath → escape blast radius;
- mutable image tag → supply-chain ambiguity;
- cluster-admin Runner → CI compromise becomes cluster compromise;
- certificate without trust validation → false sense of security.

## Вопросы

- **Secret и password?** Password — один тип secret; token, certificate и private key имеют разные semantics и rotation.
- **Почему `sensitive=true` не защищает state?** Он скрывает вывод, но значение может попасть в state.
- **Что проверять при x509?** Chain, SAN/hostname, SNI, expiry, clock и trust store конкретного client.

## 30–60 секунд

«Я разделяю identity, authorization, encryption и audit. Secret должен иметь ограниченный scope, TTL/rotation и не попадать в Git, image или logs. В Kubernetes это RBAC/ServiceAccount/Secret/NetworkPolicy, в delivery — protected variables, image/dependency/secret scanning, non-root и immutable digest».

[[02-Junior/IaC/Terraform-Ansible]], [[03-JuniorPlus/Kubernetes/Storage-Config-Secrets-RBAC]], [[05-Production-Troubleshooting/Universal-Method]].

## Термины security

Blast radius — масштаб ущерба после компрометации identity или компонента. Least privilege — минимальный набор прав для операции. Rotation — замена credential до или после компрометации. Fencing — механизм, который не даёт старому владельцу продолжать запись после failover.

## Secret lifecycle

```text
создание → хранение → выдача по policy → использование
→ аудит → rotation/revocation → удаление
```

Secret в Git нельзя «исправить новым commit»: он остаётся в history, caches и clone. Нужны revoke/rotation, очистка history по policy и проверка downstream.

## Практическое задание

1. Создай ServiceAccount только с `get` одного resource в одном namespace.
2. Проверь `auth can-i` до и после изменения Role.
3. Сгенерируй TLS certificate с неверным SAN и диагностируй x509.
4. Проверь, где secret виден: manifest, environment, process inspection, logs.
5. Добавь image/dependency/secret scan и намеренно добейся каждого gate failure.
