# HTTPS, SNI и rotation сертификата

## Цель

Научиться эксплуатировать HTTPS endpoint: выбрать сертификат по SNI, проверить цепочку и выполнить rotation без незапланированного простоя.

## Prerequisites

TLS, SNI, PKI, DNS и reverse proxy basics.

## In scope

HTTPS — HTTP поверх TLS. Listener может обслуживать несколько доменов на одном IP, выбирая certificate по SNI. Сервер должен отдавать leaf и необходимые intermediate certificates, а private key должен быть доступен только процессу или secret delivery mechanism.

Надёжная rotation:

1. выпустить новый сертификат заранее;
2. проверить SAN, срок, key usage и цепочку;
3. доставить certificate/key с корректными правами;
4. выполнить config test;
5. сделать reload, а не полный stop;
6. проверить все SNI endpoints;
7. удалить старый ключ после подтверждения.

```bash
openssl s_client -connect app.example.test:443 -servername app.example.test
openssl x509 -in server.crt -noout -dates -issuer -subject
```

## Диагностика

Различайте `unknown CA`, hostname mismatch, expired, incomplete chain, wrong SNI и protocol/cipher mismatch. Проверяйте endpoint с нескольких client trust stores.

## Типичные ошибки

- заменить сертификат без reload;
- установить цепочку в неправильном порядке;
- проверить только default virtual host;
- хранить private key в world-readable каталоге;
- отключить verify в клиенте вместо исправления chain.

## Практика

Настройте два HTTPS virtual host, выполните rotation одного сертификата, намеренно оставьте старый chain и найдите ошибку через `openssl s_client`.

## Следующие темы

`03-Reverse-Proxy-Operations.md`, security certificate lifecycle и production rotation.
