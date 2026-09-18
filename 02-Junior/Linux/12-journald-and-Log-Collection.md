# journald и сбор логов

## Цель

Научиться находить события systemd и приложений, понимая retention, structured fields и границы локального журнала.

## Prerequisites

systemd services, процессы, text streams и time synchronization.

## In scope

`journald` принимает сообщения от kernel, service stdout/stderr, syslog и native journal API. Запись имеет timestamp, unit, PID, UID, priority, boot ID и другие поля. Поэтому фильтрация по unit и boot надёжнее, чем поиск строки во всём файле.

```bash
journalctl -u nginx --since '1 hour ago'
journalctl -b -1 -p warning
journalctl -f -o short-precise
journalctl _PID=1234
```

Журнал может храниться volatile или persistent. Retention ограничивается размером, временем и свободным местом. Для production journald часто является локальным буфером, а не единственным архивом: logs должны доставляться в централизованную систему с контролем потерь и задержки.

Секреты могут попасть в stdout, environment dump или exception. Log policy должна учитывать redaction, access control и retention.

## Диагностика

Сначала ограничивайте время, boot, unit и priority. Затем сопоставляйте journal timestamp с application request ID, metrics и событиями kernel.

## Типичные ошибки

- искать только текст, не проверяя unit и boot;
- считать отсутствие записи доказательством отсутствия события;
- не ограничивать journal size;
- отправлять секреты в stdout;
- очищать журнал во время расследования.

## Практика

Создайте сервис с structured log, найдите его по unit/PID, настройте persistent storage и проверьте поведение после reboot.

## Следующие темы

`13-Log-Rotation-and-Log-Lifecycle.md`, observability и troubleshooting.
