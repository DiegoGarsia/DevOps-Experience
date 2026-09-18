# HTTP: запросы и ответы

## Цель

Понимать HTTP как application protocol и уметь читать запрос, response status, headers и body при диагностике сервиса.

## Prerequisites

TCP, ports, DNS и базовая инкапсуляция.

## In scope

HTTP-сообщение состоит из method, target, headers и optional body. Ответ содержит status code, headers и body. HTTP не гарантирует, что backend успешно выполнил бизнес-операцию: `200` может содержать ошибку внутри тела, а `202` означает принятие работы, а не завершение.

Методы имеют разную семантику:

- `GET` получает ресурс;
- `POST` создаёт или запускает обработку;
- `PUT` заменяет представление ресурса;
- `PATCH` изменяет часть ресурса;
- `DELETE` удаляет ресурс;
- `HEAD` проверяет headers без body.

Status codes группируются как 2xx success, 3xx redirection, 4xx ошибка запроса или доступа, 5xx ошибка обработки на стороне сервера или upstream. Заголовки передают cache policy, content type, correlation data, authentication context и ограничения.

HTTP/1.1 использует persistent connections, HTTP/2 мультиплексирует streams поверх одного TCP-соединения, HTTP/3 работает поверх QUIC/UDP. На этом уровне важно понимать контракт, а не детали реализации каждого протокола.

## Диагностика

```bash
curl -v https://example.org/health
curl -i -X POST -H 'Content-Type: application/json' --data '{}' https://example.org/api
```

Фиксируйте method, URL, status, response headers, latency, размер и request ID. Секреты и персональные данные из verbose output нужно удалять перед публикацией.

## Типичные ошибки

- считать любой 2xx доказательством завершённой бизнес-операции;
- терять Host header при reverse proxy;
- не учитывать redirect;
- менять timeout на client, не понимая upstream timeout;
- логировать токены в полном виде.

## Практика

Создайте маленький HTTP endpoint, проверьте методы, status codes, redirect и заголовки cache. Сопоставьте client timing с логом сервера.

## Следующие темы

`11-API-Fundamentals-and-Contracts.md`, `12-HTTP-Connection-Lifecycle-and-Timeouts.md` и TLS.
