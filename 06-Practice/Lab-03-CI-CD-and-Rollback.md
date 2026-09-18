# Лаборатория 03: CI/CD и rollback

## Цель

Построить pipeline build once → scan → registry → deploy → verify → rollback.

## Шаги

1. Создай pipeline с test, security, build, deploy и verify jobs.
2. Передай версию через artifact/dotenv, а не через случайный shell export.
3. Собери image с immutable tag и проверь digest.
4. Добавь security gate с намеренной уязвимостью.
5. Разверни две версии через rolling или canary routing.
6. Сломай readiness или application endpoint после deploy.
7. Автоматически останови promotion и верни known-good digest.
8. Проверь user path, metrics, logs и pipeline audit.

## Критерий

Rollback должен быть быстрее и надёжнее rebuild. Зафиксируй, что именно обнаружило failure, почему gate сработал и какое изменение предотвращает повтор.

[[02-Junior/CI-CD/GitLab-Pipeline-Runner]], [[04-Middle/Architecture/Delivery-Architecture-and-Tradeoffs]], [[05-Production-Troubleshooting/CI-CD]].

## Ожидаемые результаты

- downstream job получает ровно ту версию, которая была создана producer job;
- Registry digest совпадает с digest в deployed Pod;
- security failure останавливает pipeline до deploy;
- health gate замечает плохую версию, а rollback возвращает старую;
- повтор pipeline не создаёт два concurrent production changes.

## Намеренно сломанные сценарии

1. Не передай dotenv artifact → downstream version empty.
2. Опубликуй mutable tag и измени его bytes → докажи проблему audit/rollback.
3. Удали registry permission → получи auth failure.
4. Используй плохой CA → получи TLS failure.
5. Сделай endpoint возвращающим 500 при readiness 200 → проверь, почему одной probe недостаточно.
6. Запусти два deploy одновременно без lock → зафиксируй race condition и добавь `resource_group`.

## Definition of Done

Pipeline показывает version/digest, каждый gate имеет ожидаемый failure, rollback проверен через user path, а prevention привязан к конкретному обнаруженному gap.
