# Модели proxy и forwarding

## Цель

Различать forward proxy и reverse proxy и понимать, как промежуточный компонент меняет routing, headers, TLS и failure behavior.

## Prerequisites

HTTP, TLS, DNS и TCP connection lifecycle.

## In scope

Forward proxy действует от имени клиента и обычно контролирует исходящий доступ. Reverse proxy принимает запрос от клиента и выбирает backend. Он может завершать TLS, изменять headers, сжимать ответ, кэшировать, ограничивать rate и скрывать внутреннюю топологию.

```text
Forward:
Client → Proxy → Internet service

Reverse:
Client → Reverse proxy → Backend pool
```

Reverse proxy должен корректно передавать исходный client IP, Host, scheme и request ID через согласованные headers. Доверять `X-Forwarded-For` можно только от известных proxy; иначе клиент сам подделает адрес.

TLS termination уменьшает нагрузку на backend, но создаёт security boundary. Если нужен end-to-end encryption, между proxy и backend требуется отдельный TLS или mTLS.

## Диагностика

Проверяйте access log proxy и backend, upstream connect time, response time, selected backend, status и forwarded headers. Сравнивайте прямой запрос к backend с запросом через proxy.

## Типичные ошибки

- бесконечная proxy chain;
- неверный Host и выбор не того virtual host;
- потеря исходного IP;
- proxy timeout меньше допустимого backend времени;
- открытый forward proxy;
- TLS termination без защиты участка proxy–backend.

## Практика

Поставьте reverse proxy перед двумя backend, добавьте request ID и проверку health, затем намеренно выключите один backend и наблюдайте routing и logs.

## Следующие темы

`16-Load-Balancing-and-Health-Checks.md`, HTTPS operations и Kubernetes Ingress.
