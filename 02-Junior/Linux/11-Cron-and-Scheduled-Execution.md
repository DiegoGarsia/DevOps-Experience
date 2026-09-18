# Cron и запланированное выполнение

## Цель

Понимать модель cron и не переносить предположения интерактивного shell в scheduled job.

## Prerequisites

Shell startup, environment, paths и exit codes.

## In scope

Cron запускает команду по расписанию через cron daemon. Crontab имеет поля minute, hour, day of month, month и day of week. Время, environment, `PATH`, working directory и доступные права отличаются от интерактивной сессии.

Надёжное cron-задание должно:

- использовать абсолютные пути;
- задавать нужное окружение;
- перенаправлять stdout/stderr;
- иметь lock или защиту от overlap;
- быть идемпотентным;
- оставлять понятный результат.

Для новых Linux-сервисов `systemd timer` часто удобнее из-за unit dependencies, journal и resource controls. Но cron остаётся распространённым, поэтому его поведение необходимо уметь диагностировать.

## Диагностика

Проверяйте crontab пользователя, cron daemon status, timezone, права скрипта, абсолютный `PATH`, mail/output и журнал запуска. Запускайте команду от того же пользователя с очищенным environment.

## Типичные ошибки

- относительный путь к файлу;
- секрет доступен только в `.bashrc`;
- команда работает вручную, но не из cron;
- overlapping backup jobs;
- отсутствие логирования результата.

## Практика

Создайте cron job, который пишет heartbeat, затем уберите одну обязательную переменную окружения и найдите разницу между ручным и scheduled запуском.

## Следующие темы

`12-journald-and-Log-Collection.md`, `13-Log-Rotation-and-Log-Lifecycle.md` и timers.
