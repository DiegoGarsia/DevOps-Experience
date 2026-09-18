# Эксплуатация reverse proxy

## Цель

Понять, как reverse proxy принимает внешний запрос и превращает его в upstream request, сохраняя корректные headers, timeout и TLS semantics.

## Prerequisites

HTTP, TLS, DNS, proxy model и load balancing.

## In scope

Reverse proxy может завершать TLS, выбирать upstream, ограничивать размер body, задавать timeout, добавлять request ID, кэшировать или сжимать ответ. Конфигурация должна явно определять:

- слушающий address/port;
- virtual host и SNI;
- upstream address;
- forwarded headers;
- connect/read/write timeout;
- health и retry policy;
- access/error logs.

Разница между client timeout и upstream timeout важна: proxy может вернуть 504, хотя backend продолжит обработку. Retry на proxy безопасен только для операций с подходящей семантикой.

## Диагностика

Сопоставляйте request ID в client, proxy и backend. Проверяйте, был ли запрос принят, выбран ли upstream, сколько заняли connect/response и какой именно status вернул proxy.

```bash
curl -vk --resolve app.example.test:443:127.0.0.1 https://app.example.test/health
```

## Типичные ошибки

- неверный Host/SNI;
- потеря `X-Forwarded-Proto` и redirect loop;
- слишком маленький body limit;
- одинаковый timeout для коротких и long-running операций;
- логирование токенов и персональных данных;
- reload конфигурации без config test.

## Практика

Разверните proxy перед двумя backend, добавьте HTTPS и request ID, затем создайте отдельные ошибки 404, 502 и 504 и объясните их по логам.

## Следующие темы

`04-Load-Balancer-Operations-and-Health-Checks.md`, Docker networking и Kubernetes Ingress.
