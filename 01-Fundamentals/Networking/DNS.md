# DNS: resolver, zone и caching

## Что это

DNS — распределённая система имён. Stub resolver на узле отправляет запрос recursive resolver, который использует cache или получает ответ по цепочке root → TLD → authoritative server.

```text
приложение → libc/stub → recursive resolver → authoritative server
                         ← answer + TTL/cache
```

Authoritative server владеет zone data. В зоне встречаются `A/AAAA`, `CNAME`, `NS`, `SOA`, `MX`, `TXT`, `PTR`.

## Зачем это нужно

Имя отделяет service identity от изменяемого IP и позволяет discovery, delegation и controlled migration. Без DNS клиенты хранили бы адреса вручную.

## Механика

Resolver выбирает server через `/etc/resolv.conf` или systemd-resolved routing domains. TTL ограничивает положительный cache. Negative caching сохраняет NXDOMAIN/SERVFAIL с собственным TTL, поэтому исправленная запись может быть не видна сразу. DNS обычно UDP 53, но TCP нужен для больших ответов и fallback.

Split DNS — это policy выбора DNS по suffix: `~internal.example` направляет запросы во внутренний resolver, `~.` — default path. Это не routing VPN.

## Команды

```bash
resolvectl status
resolvectl query app.example
cat /etc/resolv.conf
dig app.example
dig @DNS_SERVER app.example +noall +answer +authority
dig +trace app.example
dig -x IP
```

`NOERROR` с answer означает успешный ответ. `NXDOMAIN` — authoritative утверждает отсутствие имени. `SERVFAIL` — resolver не смог получить или проверить ответ. Проверяй TTL, AUTHORITY и фактический server.

## Отказы

- имя не работает, IP работает → resolver/record/cache;
- один клиент не работает → local cache/link/routing domain;
- все клиенты не работают → authoritative zone/server/firewall 53;
- старый адрес → TTL или negative cache;
- задержки → resolver timeout, unreachable upstream, packet loss или DNSSEC.

## Вопросы

- **Recursive и authoritative?** Recursive ищет ответ для клиента, authoritative хранит source of truth зоны.
- **CNAME и A?** CNAME указывает на имя, A — на IPv4.
- **Почему DNS использует TCP?** Большой/truncated response, DNSSEC или zone transfer.

## Как объяснить за 30–60 секунд

«DNS — это не просто таблица hostname-IP: клиент выбирает resolver, resolver использует cache и при необходимости обращается к authoritative zone. В диагностике я смотрю actual resolver и link, затем делаю `dig` к системному и конкретному server, проверяю status/TTL и только после этого открываю TCP к полученному IP».

[[01-Fundamentals/Networking/Layers-IP-TCP-UDP]], [[01-Fundamentals/Networking/HTTP-TLS]], [[03-JuniorPlus/Kubernetes/Networking]].

## Пошаговый DNS-разбор

Для `api.example.test` client сначала смотрит локальные sources (`/etc/hosts`, NSS, cache), затем выбирает resolver. Recursive resolver проверяет cache. При miss он получает delegation и обращается к authoritative server. Ответ возвращается с TTL и кэшируется.

`NXDOMAIN` означает, что имя отсутствует в зоне. `NOERROR` без answer может означать существующую, но пустую запись. `SERVFAIL` означает, что resolver не получил валидный итог: проблема может быть в delegation, authoritative server, DNSSEC, transport или timeout.

```bash
dig api.example.test +noall +answer +authority +stats
dig @127.0.0.53 api.example.test
dig @AUTHORITATIVE_SERVER api.example.test SOA
resolvectl query api.example.test
```

Сравнивай системный запрос с прямым запросом к server. Если прямой authoritative ответ правильный, а системный неправильный, ищи cache, routing domain или recursive resolver.

## Практическое задание

1. Создай локальную zone с `A`, `CNAME`, `SOA` и `PTR`.
2. Увеличь serial и проверь reload.
3. Намеренно испорть zone syntax, используй `named-checkzone`, исправь ошибку.
4. Настрой Split DNS для внутреннего suffix и внешнего resolver.
5. Измени IP и наблюдай stale cache в течение TTL.
6. Сломай доступ к UDP 53 и проверь fallback/ошибку TCP.

## Production implications

- низкий TTL облегчает migration, но увеличивает query load;
- высокий TTL снижает нагрузку, но продлевает ошибочный address;
- negative caching часто объясняет, почему исправление «не работает сразу»;
- DNS service должен иметь redundancy и monitoring latency/error rate;
- private zone нельзя случайно отдавать внешнему resolver.

## Дополнительные вопросы

- Чем TTL записи отличается от timeout запроса?
- Почему DNS cache может быть корректным, но application всё ещё ходит на старый IP?
- Когда authoritative server отвечает `NOERROR` без answer?
- Почему DNS и routing — разные planes?
