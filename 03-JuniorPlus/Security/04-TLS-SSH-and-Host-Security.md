# TLS, SSH и host security

## Цель

Связать transport protection с hardening Linux host и безопасным remote access.

## Prerequisites

TLS/PKI, SSH architecture, users/permissions, firewall и secrets.

## In scope

Host baseline включает patching, минимальные services, least privilege, SSH key policy, restricted forwarding, firewall, time sync, audit/logging и certificate lifecycle. TLS verification и SSH host key verification нельзя отключать для удобства.

Hardening должен быть versioned и проверяемым; изменение security baseline без recovery access может отключить operator.

## Диагностика

Проверяйте effective SSH config, listening ports, users/sudo, package updates, TLS chain, time и firewall rules. Разделяйте network reachability, host authentication и application authorization.

## Типичные ошибки

- отключить host key checking;
- открыть SSH root/password в интернет;
- hardening без out-of-band access;
- сертификат обновлён, но процесс не reload;
- firewall change без rollback.

## Практика

Создайте host baseline, ограничьте SSH через jump host, установите test certificate и выполните audit до и после изменения.

## Следующие темы

`05-Container-and-Image-Security.md`, image supply chain и Linux hardening.
