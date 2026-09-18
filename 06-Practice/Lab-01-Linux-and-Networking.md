# Лаборатория 01: Linux и сети

## Цель

Доказать путь от процесса до HTTP и научиться ломать каждый слой отдельно.

## Шаги

1. Запусти простой сервис под отдельным пользователем через systemd.
2. Проверь PID, parent, environment, open files и listening socket.
3. Ограничь доступ firewall и проверь разницу `timeout`/`refused`.
4. Настрой локальное DNS-имя и измени его TTL.
5. Проверь `dig`, `nc`, `openssl s_client`, `curl -v`.
6. Измени сертификат так, чтобы получить hostname mismatch, затем исправь.
7. Сгенерируй diagnostic bundle: `uptime`, `free`, `df`, `ss`, route, DNS, journal.

## Критерий

Ты должен назвать слой отказа до изменения конфигурации и объяснить, почему предыдущий тест не доказывал исправность следующего слоя.

[[01-Fundamentals/Linux/Kernel-Processes-Memory]], [[01-Fundamentals/Networking/HTTP-TLS]], [[05-Production-Troubleshooting/Universal-Method]].

## Формат выполнения

Для каждого шага запиши: hypothesis, command, expected result, actual result и interpretation. Не исправляй сразу: сначала сохрани evidence.

## Ожидаемые результаты

- после запуска service `systemctl is-active` возвращает `active`, а `ss` показывает ожидаемый listener;
- при bind на loopback remote `nc` получает отказ/timeout, но local curl работает;
- при DNS failure `dig` показывает error, но direct request по IP может работать;
- при неверном CA TCP устанавливается, но TLS verification завершается ошибкой;
- после восстановления нужно проверить полный HTTP user path, а не только локальный process.

## Намеренно сломанные сценарии

1. Укажи несуществующий `ExecStart` и найди первичную ошибку в journal.
2. Измени permissions на `authorized_keys` и диагностируй `publickey`.
3. Добавь неверную DNS-запись и отличи NXDOMAIN от TCP failure.
4. Поставь firewall `DROP` только на тестовом узле и сравни packet capture с `REJECT`.
5. Измени SAN certificate и докажи, что проблема находится в TLS.

## Definition of Done

Ты можешь объяснить, какая команда проверяет какой слой, почему результат ожидаемый, чем workaround отличается от permanent fix и какое prevention нужно добавить.
