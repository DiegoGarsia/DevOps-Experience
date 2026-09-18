# Handlers и уведомление об изменении

## Цель

Понять, как запускать reload/restart только после фактического изменения конфигурации и не создавать лишний downtime.

## Prerequisites

Tasks, templates, service lifecycle и idempotency.

## In scope

Task может вызвать handler через `notify`, а handler обычно выполняется в конце play. Это разделяет изменение файла и действие, которое делает сервису видимой новую конфигурацию.

Handler должен выполнить config validation перед reload. Если несколько tasks уведомляют один handler, он обычно выполняется один раз, что защищает от серии лишних reload.

Immediate handler возможен, но его следует применять осторожно: он усложняет порядок и failure handling.

## Диагностика

Проверяйте, была ли task `changed`, дошло ли уведомление до handler, выполнился ли config test и какой status возвращён service manager.

## Типичные ошибки

- reload запускается при каждом playbook run;
- handler не вызывается из-за неправильного имени;
- service reload без validation;
- handler скрывает основную ошибку;
- restart используется там, где достаточно reload.

## Практика

Создайте template + handler для reload web service, выполните playbook дважды и убедитесь, что второй запуск не перезапускает сервис.

## Следующие темы

`06-Roles-and-Collections.md`, idempotency и testing.
