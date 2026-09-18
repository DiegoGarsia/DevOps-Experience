# Linux: пользователи, systemd и SSH

## Пользователи и права

Ядро проверяет `uid`, дополнительные группы, mode bits, ACL, capabilities и security policy перед доступом к объекту. Для directory право `x` означает возможность пройти внутрь, а не прочитать список.

```bash
id USER
namei -l /path/to/file
stat /path/to/file
getfacl /path/to/file
sudo -l -U USER
```

`sudo` делегирует команды через policy. Широкое `NOPASSWD: ALL` увеличивает blast radius компрометации.

## systemd

`systemd` — PID 1 и менеджер зависимостей. Unit бывает service, socket, timer, mount или target. `After/Before` задают порядок, `Requires/Wants` — зависимости, `Restart` — реакцию на завершение.

```bash
systemctl status SERVICE
systemctl cat SERVICE
systemctl show SERVICE
systemctl list-dependencies SERVICE
journalctl -u SERVICE -b
systemd-analyze verify /etc/systemd/system/app.service
systemctl --failed
```

После изменения unit: `daemon-reload`, затем controlled restart/reload. `status` показывает состояние, но причина обычно находится в journal.

## SSH

SSH устанавливает TCP-соединение, согласует криптографию, аутентифицирует пользователя и запускает command/shell. Публичный ключ находится на сервере, приватный остаётся у клиента.

```bash
ssh -vvv -i ~/.ssh/id_ed25519 user@host
ssh-keygen -t ed25519
ssh-copy-id user@host
sudo sshd -t
sudo sshd -T
journalctl -u ssh -u sshd
```

Перед изменением `sshd_config` нужен резервный console access, новая проверенная key-сессия и `sshd -t`.

## Почему окружение отличается

Login shell читает profile files, systemd получает окружение unit, а automation может не иметь PATH, HOME, TTY и aliases. Production unit использует absolute paths и явный `EnvironmentFile`.

## Отказы

- сервис работает вручную: неверные `User`, `WorkingDirectory`, PATH, environment или dependencies;
- `Permission denied`: проверяй весь путь через `namei`, а не только последний файл;
- SSH timeout: DNS/route/firewall/listener;
- `publickey`: ключ, права, пользователь, effective config и server journal;
- restart loop: exit code, `Restart`, start limit и application error.

## Вопросы

- **Запускает ли `After` зависимость?** Нет, только задаёт порядок; запуск требует `Requires` или `Wants`.
- **Почему нужен `sshd -t`?** Чтобы не применить неверный конфиг и не потерять доступ.
- **Почему перезапускать только при изменении?** Это уменьшает disruption и делает automation идемпотентной.

## Как объяснить за 30–60 секунд

«Сервисный incident я разделяю на unit graph, process credentials, environment, logs и listener. systemd управляет lifecycle, но не исправляет application config. В SSH сначала отличаю network timeout от authentication failure и сохраняю out-of-band access перед изменением policy».

[[01-Fundamentals/Linux/Kernel-Processes-Memory]], [[01-Fundamentals/Linux/Bash]], [[02-Junior/IaC/Terraform-Ansible]].

## Учебный разбор systemd

### Как service запускается после boot

1. PID 1 читает unit files.
2. Target формирует dependency graph.
3. `Wants/Requires` включают units, `After` определяет порядок.
4. systemd создаёт process с заданным `User`, `Group`, `WorkingDirectory` и environment.
5. stdout/stderr направляются в journal или указанное место.
6. systemd наблюдает exit status и применяет `Restart`/start limit.

Важно: `After=network.target` не доказывает, что DNS или конкретная database готовы. Для readiness нужен отдельный механизм retry/health check.

### Практический unit

```ini
[Unit]
Description=Пример приложения
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=app
WorkingDirectory=/srv/app
EnvironmentFile=-/etc/app/app.env
ExecStart=/usr/local/bin/app --config /etc/app/config.yaml
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemd-analyze verify /etc/systemd/system/app.service
sudo systemctl daemon-reload
sudo systemctl enable --now app.service
systemctl is-active app.service
systemctl show app.service -p User -p Environment -p ExecMainStatus
journalctl -u app.service -b -n 100 --no-pager
```

Ожидаемый результат `active` и `ExecMainStatus=0`. Если unit failed, ищи первую meaningful error в journal, а не последнюю строку `Start request repeated too quickly`.

### Timer и race condition

`OnUnitActiveSec=5min` запускает service относительно последнего запуска. Если задача может длиться дольше интервала, два экземпляра могут пересекаться. `flock`, `Type=oneshot` плюс проверка состояния или правильный timer design предотвращают race condition — зависимость результата от порядка конкурентных запусков.

### SSH: путь аутентификации

```text
DNS → TCP/22 → SSH handshake → server policy
→ user lookup → authorized_keys → signature proof → shell
```

`ssh -vvv` показывает этап, на котором процесс остановился. `Permission denied (publickey)` обычно означает, что сеть уже пройдена, но server не принял key/user/policy. Timeout не говорит о неправильном ключе: до authentication дело могло не дойти.

### Практическое задание

1. Создай непривилегированного user и unit.
2. Сделай ошибку в `ExecStart`, найди её в journal.
3. Сделай ошибку в `WorkingDirectory`, отличи её от `Permission denied`.
4. Добавь timer, запрети overlap через `flock`.
5. Проверь SSH в новой сессии, затем измени `PasswordAuthentication` только после `sshd -t`.
6. Намеренно испорть права `authorized_keys` и восстанови доступ через console.

### Production implications

- неверный unit environment создаёт расхождение «вручную работает, после reboot нет»;
- restart loop может перегрузить dependency и скрыть root cause;
- SSH lockout — change management failure, если нет out-of-band access;
- journal retention и timestamp нужны для postmortem.

### Дополнительные вопросы

- Чем `Requires` отличается от `Wants` при остановке dependency?
- Почему `network-online.target` не гарантирует доступность PostgreSQL?
- Как `EnvironmentFile` влияет на secret exposure?
- Почему reload безопаснее restart, но доступен не для каждого приложения?
