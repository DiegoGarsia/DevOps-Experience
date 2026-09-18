# systemd services и dependencies

## Цель

Научиться описывать порядок и условия запуска сервисов без превращения unit-файлов в скрытый shell-скрипт.

## Prerequisites

Unit lifecycle и boot process.

## In scope

`Requires`, `Wants`, `After`, `Before`, `BindsTo` и `PartOf` выражают разные отношения. Dependency отвечает, какие units должны быть активированы или остановлены, а ordering определяет порядок, но не создаёт dependency сам по себе.

`Requires=network.target` не означает, что сеть уже получила IP и DNS готов. Для сервисов, которым нужна рабочая сеть, используется соответствующая readiness semantics distribution, а приложение всё равно должно обрабатывать временную недоступность зависимости.

`Restart=on-failure`, `RestartSec`, `StartLimitIntervalSec` и `StartLimitBurst` управляют retry самого service manager. Они не заменяют исправление crash loop и не должны создавать бесконечную нагрузку.

```ini
[Unit]
Description=Example API
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/example-api
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

## Диагностика

```bash
systemctl list-dependencies example.service
systemctl is-enabled example.service
systemctl show -p After,Wants,Requires example.service
systemctl reset-failed example.service
```

Анализируйте dependency graph и журнал одного запуска. Не увеличивайте restart limit, пока не поняли причину exit.

## Типичные ошибки

- путать `After` с `Requires`;
- использовать sleep вместо зависимости или readiness;
- добавить `Restart=always` для неисправного процесса;
- запускать service от root без необходимости;
- не ограничить resource usage.

## Практика

Создайте два связанных service units, намеренно нарушьте порядок старта, затем исправьте dependency и добавьте controlled restart policy.

## Следующие темы

`09-systemd-Targets.md`, `10-systemd-Timers.md` и journald.
