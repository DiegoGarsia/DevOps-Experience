# HTTP, TLS и reverse proxy

## HTTP

HTTP — stateless request/response protocol поверх transport. Request содержит method, target, headers и body; response — status, headers и body. Классы status: 2xx — успех, 3xx — redirect, 4xx — проблема request/auth, 5xx — server/upstream.

HTTP/1.1 использует persistent TCP connections, HTTP/2 multiplexes streams, HTTP/3 использует QUIC поверх UDP. Для диагностики важно понимать semantics и границы отказа.

## TLS handshake

```text
ClientHello(SNI, versions, ciphers)
→ ServerHello + certificate chain
→ проверка SAN, signature, expiry, trusted CA
→ key exchange
→ encrypted HTTP
```

TLS authenticates endpoint и шифрует traffic, но не гарантирует здоровье приложения. SNI помогает выбрать сертификат на общем listener. Internal CA работает только там, где trusted root/intermediate установлен.

## Reverse proxy

```text
client → DNS/TCP/TLS → proxy → routing rule → upstream → application
```

Proxy может завершить TLS, выбрать upstream, добавить `Host`/`X-Forwarded-*`, применить timeout и load balancing. Health check должен проверять тот уровень, о котором заявляет.

## Команды

```bash
curl -v http://HOST/path
curl -vk https://HOST/path
curl -I https://HOST/path
curl -w 'dns=%{time_namelookup} connect=%{time_connect} tls=%{time_appconnect} ttfb=%{time_starttransfer}\n' -o /dev/null https://HOST
openssl s_client -connect HOST:443 -servername HOST </dev/null
```

`-k` временно отключает проверку доверия и не является исправлением.

## Матрица отказов

| Симптом | Слой |
|---|---|
| NXDOMAIN/SERVFAIL | DNS |
| connect timeout/refused | route/firewall/listener |
| certificate verify/SAN error | TLS/CA/SNI/time |
| 301/302 неожиданен | HTTP/proxy policy |
| 401/403 | auth/authorization/application |
| 502/504 | proxy-to-upstream |
| 500 | application/dependency |

## Вопросы

- **TLS termination и passthrough?** Termination расшифровывает на proxy и требует key там; passthrough сохраняет end-to-end TLS, но ограничивает L7 inspection.
- **Почему 502 не равен 500?** 502 означает, что proxy не получил корректный ответ upstream; 500 — ответ, сформированный приложением или proxy.
- **Зачем `X-Forwarded-Proto`?** Для корректных redirect, secure cookies и генерации URL.

## Как объяснить за 30–60 секунд

«Запрос проходит DNS, TCP, TLS, HTTP, proxy, upstream и application. TLS проверяет identity и шифрует канал, а reverse proxy принимает routing и timeout decisions. Поэтому x509, timeout, 502 и 500 не лечатся одним перезапуском».

[[01-Fundamentals/Networking/DNS]], [[01-Fundamentals/Networking/Firewall-NAT-Proxy]], [[03-JuniorPlus/Kubernetes/Networking]].

## HTTP request и response: интерпретация

```http
GET /health HTTP/1.1
Host: app.example.test
Accept: application/json
```

Proxy и application могут выбирать route по Host, method, path и headers. `Content-Length`/chunked transfer определяют границы body. Timeout на connect, send, read и total означает разные точки отказа.

### Статусы как подсказка

- 301/302: redirect policy, проверь Location и возможный redirect loop;
- 401: отсутствует или неверна authentication;
- 403: identity известна, но action запрещён;
- 404: route/resource не найден, проверь Host/path и upstream;
- 429: rate limit/backpressure;
- 502: proxy не получил корректный ответ upstream;
- 503: service unavailable/no healthy upstream;
- 504: upstream timeout;
- 500: application/server error.

## TLS verification

```bash
openssl s_client -connect app.example.test:443 -servername app.example.test -showcerts </dev/null
curl --cacert root-ca.pem https://app.example.test/health
```

Смотри `subject`, `issuer`, SAN, validity, цепочку intermediate и negotiated protocol. Если `curl -k` работает, а обычный `curl` нет, проблема в trust/hostname/time, а не в HTTP application.

## Практическое задание

1. Подними простой upstream и reverse proxy.
2. Настрой HTTP→HTTPS redirect.
3. Сделай отдельные ответы 401, 403, 404, 500 и timeout.
4. Проверь direct upstream и proxy path.
5. Сними TLS с неверным SAN, истёкшим сертификатом и неизвестным CA.
6. Настрой timeout и upstream health check, затем останови backend.

## Production implications

- proxy должен корректно передавать source identity и original scheme;
- неправильный retry на POST может создать duplicate side effect;
- timeout слишком большой удерживает connections, слишком малый создаёт ложные 504;
- certificate rotation требует overlap старого и нового trust chain;
- access/error logs должны содержать request id, upstream status и duration.

## Дополнительные вопросы

- Почему 502 и 504 не являются доказательством ошибки приложения?
- Как отличить redirect loop от DNS loop?
- Где TLS termination безопаснее, а где нужен passthrough?
- Почему HTTP status и TCP status нельзя смешивать?
