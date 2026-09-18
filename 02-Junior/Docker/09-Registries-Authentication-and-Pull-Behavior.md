# Registries, authentication и pull behavior

## Цель

Понять путь image от builder до runtime и безопасно организовать доступ к registry.

## Prerequisites

OCI images, tags/digests, Docker daemon и TLS/PKI.

## In scope

Registry хранит manifests, configs и layers и отдаёт их по HTTP API. Клиент может скачать только отсутствующие layers, поэтому одинаковые base layers экономят трафик.

Authentication подтверждает identity, authorization определяет доступ к repository и operation. Credentials должны доставляться через secret mechanism и не попадать в command history или logs. TLS защищает channel, но не заменяет проверку image provenance.

Pull behavior зависит от tag policy, digest, architecture, cache, registry availability и rate limits. В production используйте immutable digest и заранее проверяйте доступность критических images.

## Диагностика

Разделяйте DNS/connect/TLS, authentication `401`, authorization `403`, missing manifest `404`, rate limit и повреждённый layer. Проверяйте daemon logs и registry logs с correlation data.

## Типичные ошибки

- `latest` в production;
- общий registry credential на все nodes;
- отсутствие image pull secret rotation;
- отсутствие cache policy;
- игнорирование architecture mismatch.

## Практика

Разверните test registry с TLS, push image по digest, настройте read-only pull credential, отзовите его и диагностируйте ожидаемый отказ.

## Следующие темы

`10-Docker-Security.md`, `11-Docker-Troubleshooting.md` и CI/CD image pipeline.
