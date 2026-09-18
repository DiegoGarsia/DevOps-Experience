# DNS: путь разрешения и resolvers

## Цель

Понять, как имя превращается в IP-адрес, какие участники участвуют в запросе и где возникает задержка или неправильный ответ.

## Prerequisites

IP, routing, UDP/TCP и sockets.

## In scope

DNS использует иерархические имена и распределённую базу. Stub resolver на host передаёт запрос configured recursive resolver. Recursive resolver при необходимости обращается к root, TLD и authoritative servers, кэшируя ответ на время TTL.

```text
Приложение
  ↓ libc / resolver library
Stub resolver
  ↓
Recursive resolver
  ├── cache
  └── root → TLD → authoritative server
  ↓
Ответ A/AAAA/CNAME и TTL
```

`/etc/resolv.conf`, systemd-resolved, NetworkManager и контейнерная runtime могут задавать разные DNS paths. Поэтому запрос с host и запрос внутри container не обязательно используют один resolver.

Рекурсивный resolver ищет ответ от имени клиента, а authoritative server отвечает за конкретную zone. Ошибка `SERVFAIL` означает проблему обработки запроса, `NXDOMAIN` — авторитетное утверждение, что имя не существует, но подробная причина требует проверки.

## Диагностика

```bash
resolvectl status
dig example.org
dig @192.0.2.53 example.org A +trace
getent hosts example.org
```

Сравнивайте путь через system resolver и прямой запрос к указанному server. Фиксируйте тип записи, TTL, flags и время ответа.

## Типичные ошибки

- проверять только `/etc/hosts`;
- считать DNS исключительно UDP;
- игнорировать search domains;
- путать NXDOMAIN с timeout;
- менять приложение, не проверив resolver path.

## Практика

Настройте локальный recursive resolver, добавьте test zone, сравните cold-cache и warm-cache запросы, затем сломайте один upstream и определите наблюдаемую ошибку.

## Следующие темы

`09-DNS-Caching-TTL-and-Record-Behavior.md`, HTTP и TLS.
