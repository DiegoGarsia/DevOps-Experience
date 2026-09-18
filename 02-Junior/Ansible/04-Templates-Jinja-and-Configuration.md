# Templates, Jinja и configuration

## Цель

Научиться генерировать конфигурацию по данным host, сохраняя валидность, права и безопасную обработку secrets.

## Prerequisites

Variables, facts, YAML, service configuration и idempotency.

## In scope

Template — функция от variables/facts к текстовому файлу. Результат должен быть валидирован до reload сервиса. Jinja поддерживает conditions, loops, filters и tests, но сложную бизнес-логику лучше вынести из template.

```jinja2
server {
  listen {{ app_port }};
  {% if enable_tls %}
  ssl_certificate {{ tls_cert_path }};
  {% endif %}
}
```

После изменения template task должна уведомить handler, который выполнит config test и reload. Secret должен попадать в template контролируемо и не отображаться в diff/log без необходимости.

## Типичные ошибки

- невалидная конфигурация из-за whitespace;
- template содержит слишком много условий;
- reload до config test;
- `no_log` скрывает слишком большой контекст;
- права файла создают доступ к secret.

## Практика

Сгенерируйте конфигурацию reverse proxy для двух environments, добавьте TLS condition, запустите lint/config test и проверьте режим файла.

## Следующие темы

`05-Handlers-and-Change-Notification.md`, roles и idempotency.
