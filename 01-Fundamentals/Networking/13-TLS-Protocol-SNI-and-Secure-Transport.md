# TLS, SNI и защищённый transport

## Цель

Понять, какие свойства даёт TLS и как client/server выбирают сертификат и параметры защищённого соединения.

## Prerequisites

TCP, HTTP, DNS и базовая криптографическая терминология.

## In scope

TLS создаёт защищённый канал поверх transport protocol. Он обеспечивает конфиденциальность, целостность и authentication server через certificate chain. Во время handshake стороны согласуют версию и cipher suite, выполняют key exchange и формируют session keys. Дальше application data шифруются симметричным алгоритмом.

Сертификат связывает hostname с public key и подписывается CA. Client проверяет срок действия, цепочку доверия, hostname и ограничения сертификата. Шифрование без проверки identity защищает от пассивного наблюдателя, но не обязательно от MITM.

SNI передаёт имя сервера в ClientHello до выбора сертификата. Это позволяет одному IP обслуживать несколько HTTPS-доменов. В современных сценариях ECH может скрывать часть имени, но базовая проблема выбора virtual host остаётся.

```text
ClientHello + SNI
    ↓
Server certificate
    ↓ validation against CA and hostname
Key exchange
    ↓
Encrypted HTTP data
```

## Диагностика

```bash
openssl s_client -connect example.org:443 -servername example.org -showcerts
curl -v https://example.org
```

Проверяйте фактический сертификат, SAN, issuer, срок действия, negotiated protocol и имя SNI. Ошибка TLS может находиться до отправки HTTP-запроса.

## Типичные ошибки

- отключать certificate verification как постоянное решение;
- проверять CN и игнорировать SAN;
- забывать SNI при тесте нескольких virtual hosts;
- считать self-signed сертификат автоматически небезопасным для private CA;
- не планировать rotation до истечения срока.

## Практика

Настройте два HTTPS virtual host на одном IP, проверьте выбор сертификата через SNI, затем подмените цепочку и определите, на каком шаге клиент откажется.

## Следующие темы

`14-Certificates-CA-PKI-and-Validation.md`, затем HTTPS operations.
