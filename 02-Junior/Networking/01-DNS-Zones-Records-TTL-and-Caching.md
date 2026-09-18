# DNS zones, records, TTL и caching

## Цель

Научиться администрировать небольшую DNS-зону и отличать ошибку authoritative configuration от проблемы resolver cache.

## Prerequisites

DNS resolution path, record behavior, UDP/TCP и IP routing.

## In scope

Zone — административная часть DNS namespace, за которую отвечает authoritative server. Zone file содержит SOA, NS и resource records. SOA задаёт serial и параметры refresh/retry/expire/negative caching; serial должен изменяться при обновлении зоны.

Пример:

```dns
$ORIGIN example.test.
@  3600 IN SOA ns1.example.test. hostmaster.example.test. (
       2026091701  ; serial
       3600        ; refresh
       600         ; retry
       86400       ; expire
       300 )       ; negative TTL
@  3600 IN NS ns1.example.test.
@  300  IN A 192.0.2.10
api 300 IN CNAME app.example.test.
```

Authoritative answer и recursive cache имеют разные источники. После изменения записи нужно проверить serial, reload сервера, authoritative response и TTL у recursive resolver.

## Диагностика

```bash
dig @ns1.example.test example.test SOA
dig @ns1.example.test api.example.test CNAME
dig @resolver.example.test api.example.test A
```

Ошибки syntax, missing NS, неправильный serial и недоступность authoritative server диагностируются отдельно.

## Типичные ошибки

- забыть точку в fully qualified name;
- не увеличить serial;
- рассчитывать на мгновенный cache invalidation;
- создать CNAME рядом с несовместимыми records;
- уменьшить TTL уже после распространения старого ответа.

## Практика

Создайте forward zone с A, CNAME, TXT и SOA, проверьте её lint/reload, измените адрес и измерьте propagation через два resolver.

## Следующие темы

`02-HTTPS-SNI-Certificate-Rotation-and-Validation.md`, DNS troubleshooting и reverse proxy.
