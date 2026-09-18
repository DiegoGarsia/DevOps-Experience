# HTTP connection lifecycle и timeouts

## Цель

Разделить DNS, connect, TLS, request, response и idle timeout и понимать, где именно теряется время.

## Prerequisites

TCP lifecycle, HTTP protocol и API contracts.

## In scope

Полный путь client request может включать:

```text
DNS lookup
  → TCP connect
  → TLS handshake
  → request headers/body
  → queue или upstream connect
  → server processing
  → response headers/body
  → connection reuse или close
```

У клиента и каждого proxy/backend могут быть отдельные таймауты: DNS resolution, connect, TLS handshake, request write, response header, body read, idle keepalive. Если внутренний timeout больше внешнего, клиент может закрыть соединение раньше, оставив backend работу в неопределённом для него состоянии.

Retry увеличивает нагрузку. Нужны ограничение попыток, backoff, jitter, deadline и понимание идемпотентности. Таймаут должен быть связан с budget операции, а не выбран «с запасом» без измерений.

## Диагностика

```bash
curl -w '\nlookup=%{time_namelookup} connect=%{time_connect} tls=%{time_appconnect} start=%{time_starttransfer} total=%{time_total}\n' -o /dev/null -s https://example.org
```

Сопоставляйте client timing с proxy access log, backend latency и TCP capture. Один total time не показывает слой задержки.

## Типичные ошибки

- одинаковый timeout на всех слоях;
- бесконечный retry;
- retry POST без idempotency;
- отсутствие deadline propagation;
- считать keepalive всегда полезным при перегруженном backend.

## Практика

Добавьте искусственную задержку в DNS, TLS и backend отдельно. Измерьте каждый этап, установите разные timeout и определите, какой слой первым обрывает запрос.

## Следующие темы

`13-TLS-Protocol-SNI-and-Secure-Transport.md`, proxy и load balancing.
