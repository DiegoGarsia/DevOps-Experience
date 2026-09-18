# Архитектура PKI и lifecycle сертификатов

## Цель

Спроектировать certificate lifecycle от выпуска до rotation/revocation без ручной зависимости от одного оператора.

## Prerequisites

TLS, CA/PKI, SSH, Secrets и time synchronization.

## In scope

Lifecycle включает identity/CSR, approval, issuance, delivery, validation, renewal, rotation, revocation и audit. Разделяйте root/intermediate CA, trust stores, server/client certificates и key custody.

Автоматическая renewal должна иметь запас времени, monitoring expiry, rollback старого сертификата и проверку каждого SNI endpoint.

## Практика

Создайте private CA/intermediate, выпустите сертификаты для нескольких services, выполните rotation и simulated expiry с alert.

## Следующие темы

`02-Vault-Architecture-and-Secret-Delivery.md`, supply chain и production certificate operations.
