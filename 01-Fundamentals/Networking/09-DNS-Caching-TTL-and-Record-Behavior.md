# DNS caching, TTL и поведение записей

## Цель

Понимать, почему изменение DNS не распространяется мгновенно и как TTL, тип записи и несколько уровней cache влияют на результат.

## Prerequisites

Путь DNS resolution и recursive/authoritative roles.

## In scope

TTL задаёт, сколько resolver может считать ответ пригодным для кэширования. Это не обещание точного времени обновления: существующие кэши, negative caching, application cache и connection pooling могут продлить фактическое использование старого адреса.

Основные записи:

- `A` — IPv4;
- `AAAA` — IPv6;
- `CNAME` — другое каноническое имя;
- `NS` — authoritative servers зоны;
- `MX` — mail exchange;
- `TXT` — текстовые данные и policy;
- `PTR` — reverse lookup.

При наличии нескольких адресов порядок ответа не является полноценным health check. DNS round-robin распределяет ответы, но не знает, живо ли приложение. Для controlled migration используют низкий TTL заранее, проверяют обе стороны и учитывают connection reuse.

Negative answer также кэшируется по SOA-параметрам. Поэтому исправление отсутствующей записи может не стать видимым сразу.

## Диагностика

```bash
dig example.org A +noall +answer
dig example.org CNAME +noall +answer
dig example.org SOA +noall +answer
dig -x 192.0.2.10
```

Сравнивайте authoritative answer с ответом recursive resolver и смотрите оставшийся TTL.

## Типичные ошибки

- менять запись после начала миграции, не снизив TTL заранее;
- использовать DNS как единственный health check;
- забывать AAAA при проблемах только у IPv6-клиентов;
- трактовать CNAME как IP address;
- не учитывать negative caching.

## Практика

Измените A-запись тестовой зоны, измерьте время появления изменения у разных resolvers и сравните authoritative/recursive ответы.

## Следующие темы

`10-HTTP-Protocol-Requests-and-Responses.md`, `11-API-Fundamentals-and-Contracts.md` и DNS troubleshooting.
