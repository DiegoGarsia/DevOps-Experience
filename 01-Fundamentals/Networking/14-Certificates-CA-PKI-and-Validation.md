# Сертификаты, CA, PKI и validation

## Цель

Понимать trust chain и lifecycle сертификата, чтобы диагностировать TLS без бездумного отключения проверки.

## Prerequisites

TLS protocol, SNI и public-key basics.

## In scope

PKI включает субъектов, ключевые пары, сертификаты, CA, trust stores, revocation и процессы выпуска/отзыва. Root CA обычно является trust anchor клиента, intermediate CA подписывает leaf certificate, а сервер отдаёт цепочку до trusted root или близко к нему.

Сертификат содержит subject/SAN, public key, issuer, validity period, key usage, extended key usage и подпись. Клиент проверяет:

1. подпись цепочки;
2. доверие к root;
3. соответствие hostname SAN;
4. срок действия;
5. назначение ключа;
6. при необходимости revocation status.

Private key должен храниться отдельно и иметь ограниченный доступ. Certificate rotation — операционный процесс: выдача нового сертификата, доставка, reload, проверка, удаление старого ключа и контроль срока следующего обновления.

## Диагностика

```bash
openssl x509 -in server.crt -noout -text
openssl verify -CAfile ca.pem -untrusted intermediate.pem server.crt
```

Различайте ошибки: unknown CA, hostname mismatch, expired certificate, incomplete chain и неправильный key usage требуют разных исправлений.

## Типичные ошибки

- копировать private key вместе с public certificate в общий каталог;
- отдавать клиенту только leaf без intermediate;
- обновлять файл, но не делать reload процесса;
- проверять сертификат только на одном endpoint;
- считать короткий срок действия проблемой без автоматической rotation.

## Практика

Создайте private CA и intermediate, выпустите сертификат для test hostname, проверьте цепочку, исправьте hostname mismatch и выполните controlled rotation.

## Следующие темы

`15-Proxy-Models-and-Forwarding.md`, `16-Load-Balancing-and-Health-Checks.md` и Security certificate lifecycle.
