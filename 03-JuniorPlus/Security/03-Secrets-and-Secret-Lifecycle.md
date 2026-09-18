# Secrets и secret lifecycle

## Цель

Рассматривать secret как объект с owner, scope, rotation и revocation, а не как строку в конфигурации.

## Prerequisites

Authentication, authorization, TLS, environment и CI trust boundaries.

## In scope

Lifecycle: create → deliver → use → rotate → revoke → audit → delete. Secret должен быть минимальным, короткоживущим и доставляться только нужному process. Encryption at rest не отменяет access control и redaction.

Проверьте все места копирования: Git, CI logs, process args, environment, image layers, backups, crash dumps и observability.

## Диагностика

При утечке сначала revoke/rotate, затем определить scope exposure, сохранить audit evidence и проверить downstream systems. Не ограничивайтесь удалением строки из repository.

## Типичные ошибки

- base64 принимается за encryption;
- secret lifetime не связан с service lifecycle;
- rotation без reload/restart plan;
- backup содержит ключи без отдельной защиты;
- один credential используется несколькими системами.

## Практика

Создайте test secret, доставьте его приложению, выполните rotation без публикации значения и проверьте отказ старого credential.

## Следующие темы

`04-TLS-SSH-and-Host-Security.md`, Vault и Kubernetes Secrets.
