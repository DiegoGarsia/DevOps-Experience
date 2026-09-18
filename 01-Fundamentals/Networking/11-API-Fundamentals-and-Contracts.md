# Основы API и контракты

## Цель

Понять API как контракт между потребителем и сервисом и учитывать этот контракт при delivery, observability и troubleshooting.

## Prerequisites

HTTP requests/responses и structured data.

## In scope

API определяет ресурсы или операции, формат данных, authentication, ошибки, versioning, limits и ожидаемую семантику повторного вызова. Хороший контракт отвечает: что отправить, какой ответ получить, как выглядит ошибка и можно ли безопасно повторить запрос.

Пример минимального контракта:

```yaml
method: POST
path: /v1/orders
request:
  content_type: application/json
  required: [idempotency_key, items]
response:
  success: 201
  retryable: [429, 502, 503]
```

Versioning нужен, когда изменение несовместимо с существующими клиентами. Backward compatibility важнее удобства реализации: добавление optional поля обычно безопаснее переименования или изменения типа.

Idempotency означает, что повтор операции не создаёт нежелательное дополнительное действие. Это особенно важно при timeout: клиент не знает, обработал ли сервер запрос до разрыва соединения.

## Наблюдаемость контракта

API должен иметь request ID, понятные status codes, latency/error metrics и безопасный audit trail. Ошибка должна сообщать клиенту достаточно для исправления запроса, но не раскрывать stack trace или секреты.

## Типичные ошибки

- отсутствие схемы и версий;
- разные сервисы используют один status code для разных ошибок;
- retry неидемпотентной операции;
- публикация внутренних исключений;
- изменение обязательного поля без миграции клиентов.

## Практика

Опишите API для создания заказа: request, response, ошибки, idempotency key и правила retry. Составьте тесты на совместимость старого и нового клиента.

## Следующие темы

`12-HTTP-Connection-Lifecycle-and-Timeouts.md`, TLS и distributed-system retry patterns.
